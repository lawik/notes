This series covers a lot of fundamentals about the underlying BEAM VM and Erlang as consequence of covering Elixir fundamentals. A lot has been said about The Actor Model when it comes to Erlang. That's kind of funny. Because the only thing that as a matter of terminology should definitely be called actors in the Erlang world are Bjarne Däcker, Joe Armstrong, Mike Williams and Robert Virding as featured in [Erlang: The Movie](https://www.youtube.com/watch?v=xrIjfIjssLE). And while technically correct, it is quite charitable to call them actors. I suspect they would agree.

[The Actor Model](https://en.wikipedia.org/wiki/Actor_model) is generally a model for concurrent computation. And Erlang was [not built to implement Actors](https://erlang.org/pipermail/erlang-questions/2014-June/079794.html). I'm sure we could be debate about whether it does or not. I have not read Carl Hewitt and I'm not going to offer an opinion. It does something similar enough in terms of message passing and spawning. It might be the case that Erlang processes has shifted what people expect an Actor to be and rather than be a realization of the theory it has become an implementation that overrides the theory. I will reference The Actor Model as something Erlang and Elixir does with regards to processes, messaging and concurrency for the purposes of this post. Please argue about this further in all comment sections and social media for traction and engagement ;)

Regardless. The terminology of an "actor" does not exist* in Erlang or Elixir. There is spawning processes, sending messages and receiving them. The typical representative of a high-level, full-featured actor in Erlang is the gen_server (there are also gen_event, gen_statem and other abstractions with different features) and in Elixir we have the GenServer module, etc. A generic server. They are almost exclusively started with a link to the spawning process which should ideally be a Supervisor. I cover more of this under [the resilience part of the series](/unpacking-elixir-resilience.html).

*\*  I actually recently found a use of "actor" in the ecosystem. The Ash Framework uses the term actor for the concept of "the acting party". Typically a user, team or organisation taking an action on a resource. It is unrelated and not in Elixir itself. Amusing though. Ash brought Actors to Elixir.*

A GenServer is a bunch of logic started as the core loop of an Erlang process. It conforms to a number of useful protocols for debuggability, introspection and as mentioned they tend to be started with [a link](https://www.erlang.org/doc/man/erlang#link-1). Meaning the processes will be aware if the other end of the link exits. The fundamental loop is to wait until it receives a message, execute a relevant code path based on the message, update held state if necessary, back to receive a message and repeat.

There are three main ways of interacting with a started GenServer. Calls are a request/response approach with a timeout. These are synchronous from the calling side but can be handled in an asynchronous fashion by the GenServer (see returning  a `:noreply` on a call [in the docs](https://hexdocs.pm/elixir/1.12/GenServer.html#c:handle_call/3)). Then we have a cast which is a fire-and-forget message sent to a GenServer. And then there is regular message passing with `send/2` and related fundamentals. A GenServer is an Erlang process and it can receive messages that are not calls and casts. Similarly it can send messages that are not replies to calls.

For people coming from OOP to Elixir there is often an attempt to map a class to a GenServer module and object instantiation to `start_link` and `init`. This is generally an anti-pattern. A GenServer sets the runtime behavior of your application and is typically relevant for holding a connection, processing work from a queue, polling an API or other runtime concerns. It is a system design and architectural choice. It is not really about code structure and encapsulation. If what you want is to group data and logic, what you want for this is usually a module, probably with an Elixir struct and a bunch of functions. Pure immutable operations that can be performed in any process at any time without creating a bottleneck. Your actors can benefit a lot from modules like this to manage their internal operations.

Whenever you need serial ordering or fanning out to multiple parallel pieces of execution you should reach for GenServers, Tasks and such. Sometimes you absolutely want a bottleneck, usually when you need control of how things interact. Rate limiting, serializing writes, managing a unique resources such as a GPU or tracking information about an ongoing background process.

One example of a typical actor usage is also one of the more agressive uses of the paradigm. The Phoenix LiveView. The LiveView holds a Phoenix Channels WebSocket connection in state. It handles events received from the WebSocket, messages from the system and through these handler functions it can update the state of the LiveView. Every change to the state produces diffs that can be passed to UI over the WebSocket. Every Erlang-style actor exposes an API surface to the surrounding system, the LiveView exposes a subset of it over the WebSocket.

All Erlang processes including GenServers have a process ID, succinctly known as a PID. There are numerous ways of registering, aliasing, grouping and finally addressing them. This can be used to implement messaging patterns such as queue/broker and workers or publish/subscribe (pubsub) internally for the application. Messaging can also traverse an Erlang cluster transparently which is incredibly convenient and powerful. During messaging you don't have to be concerned about where your processes are in the cluster. If you have the PID you can reach them.

Concretely, the Erlang `:pg` module handles Process groups and can register named groups across the cluster in an incredibly convenient way. This is the foundation of Phoenix PubSub. Unless you use the Redis adapter in which case you should* probably move on from Heroku to get the best use out of Elixir.

*\* I snark.*

While all* code in Erlang and Elixir runs in processes and most libraries and tools you work with are built on top of gen_server and friends. The Actor Model as implemented in Erlang is not the dominant paradigm that determines what your code looks like. Not in the way Object-Oriented Programming does. It is not like in Python where "everything is an object" kind of dominates the language. Most of Elixir is a dynamic flavor of Functional Programming. You don't spend all your time doing Actor-stuff. Processes and messaging are underpinnings for the code you write and when you need to deal with state or work needs to be distributed across more units of concurrency. Certainly. However I find most of what I do is basic Functional Programming.

*\*  There are several exceptions. It is true enough but not actually absolutely true. Ports and NIFs are ways of doing things outside of regular Erlang processes.

And as anyone who has done Elixir for a bit can attest. If you isolate the places where you perform messaging a bit from other, pure logic, that makes it a lot easier to test. Some have argued that Erlang is like Kubernetes and that GenServers are like microservices. This is very incorrect. But it may be helpful for understanding the nature of an Erlang/Elixir application. It has many things going on. It is very much as system in and of itself, it is not constrained to being a single web app or a single worker. It was designed to do many things.

Where this analogy holds well is probably testing. Writing tests across Kubernetes cluster is probably even worse than writing tests across your Elixir application but I think they are similarly instructive. To test multiple moving parts like this you need a lot of control over them from the testing side. The worst case is if they are heavily reliant on time and timing. That is really painful to work with in any system and it is quite difficult to control well.

Trying to write an integration test that crosses multiple GenServer and Supervisor boundaries will tell you how well you did with abstractions. You will know where you took shortcuts or simplified things too much. And this is the reason why the pure tests are much easier. Testing a single GenServer that can be started with a non-global name is a reasonable process. Testing a few of them that are all essentially singletons probably prevents your tests from running in parallel, will make cleanup and reset a pain and will make you want to refactor almost immediately. This has influenced the [library guidelines](https://hexdocs.pm/elixir/1.15.7/library-guidelines.html#anti-patterns) for Elixir. Under Anti-Patterns you'll find:

[Avoid application configuration](https://hexdocs.pm/elixir/1.15.7/library-guidelines.html#avoid-application-configuration)

Why? Because it is global. Your user may use it and your library might offer it as a convenience. If you require it your are placing massive limitations on how useful your library can be.

[Avoid using processes for code organization](https://hexdocs.pm/elixir/1.15.7/library-guidelines.html#avoid-using-processes-for-code-organization)

Which is what I was saying above. Processes and consequently GenServers are not a tool for code organization.

You will find that Phoenix generally generates modules for you that you then own. MyAppWeb.Endpoint, MyApp.Repo (from Ecto, really), MyApp.PubSub and so on. Then these are started in your application. You own them. If they pull config they pull them based on the name they have in your application so that if you want multiple Repos you absolutely can (dynamic repos also exist, different purpose). The Endpoint gives your the web server supervision tree, the Repo gives you the database connection pool supervision tree, the PubSub manages your process groups and whatnot for the PubSub mechanism.

Phoenix needs processes for what it does, concurrent requests, queries, pubsub. But it hands you that stuff. You can run many Phoenixes in your application if you need or want. The framework hands you the reigns.

Wrapping up. The Actor Model that Erlang actually has is a mechanism mostly intended to provide a reasonable API for concurrency. And distribution. And also a way to model a system design with a highly dynamic functional language. It does shared-nothing and message passing. This is where many (perhaps most?) high-level concurrency APIs end up. The alternatives are to my understanding always more painful with much more nuanced performance trade-offs.

If you have a need to argue about the nuances of Actors I am available explicitly for that practice (also business inquiries) over email as {{< lars_email >}} and out there on the fedi {{< lars_twitter >}}.