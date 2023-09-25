The nine nines. 99.9999999% of uptime. Whether the AXD301 actually deserves to be held up as a system of nine nines seems debatable. I am not particularly interested in that debate. Erlang has a strong record for reliability and a design intended to help you as a developer and operator achieve your nines. Maybe just five of them. Up to you really.

Previous episodes of this article series has unpacked [Concurrency](/unpacking-elixir-concurrency.html), [Real-time & Latency](/unpacking-elixir-realtime-latency.html) and [the syntax](/unpacking-elixir-syntax.html). In this one we talk about how Elixir and Erlang achieve reliability.

There are agressive, defensive and rigorous practices you can adopt in a variety of ways to achieve reliability and resilience. Testing, profiling, specifications, design, observability, culture, operations and so on. This is not about most of those. This is about software abstractions that serve you in making your system more resilient and allow you to construct resilient designs.

Erlang implements the Actor model, the typical actor in Erlang and Elixir terminology is the GenServer. The generic server. It is a process that has some nice facilities for receiving calls ("synchronous" messages, expecting a response within a timeout) and casts (asynchronous messages, with no expectation of reply). A GenServer is a bunch of API wrapped around the basic Erlang process. A process can receive and send messages. A GenServer (also other related types, gen_event, gen_statem) implements capturing errors that occur and the subsequent exit and passing information about the problems to the parental process as a message.

So what is the parental process. Well it is usually a special type of Erlang process called a Supervisor. A Supervisor can run multiple child processes that implement the necessary API for being a child. The Supervisor then handle problems with the children falling over by applying a strategy.

The strategies are normally `one_for_one`, `one_for_all` and `one_for_rest`. One for one indicates that if a child crashes it, it and only it should be considered when restarting. Leave the others alone. One for all means that the supervisor should restart all children if one child fails. This is often useful if the children are sharing a workload in some important way where restarting one would cause trouble with the others. One for rest is kind of nuanced in that the Supervisor will restart any child defined in the starting order after the crashing one. This can be used to kill a supporting process if the main one dies for example.

So a Supervisor supervises a set of child processes. This set can be dynamic, growing and shrinking as the system operates. In Elixir that would be a DynamicSupervisor. If a single crash happens within a child it will apply the strategy. If the new child crashes immediately it will try again. If there are enough failures within a given threshold the Supervisor will consider itself failing at the job and crash.

This crashing is a feature, not a bug. By crashing it will propagate a signal to whatever Supervisor started this Supervisor that there is an issue. This Supervisor can in turn apply strategies to try and recover the Supervisor through the tried and true practice of turning it off and on again. Or via custom logic if you are feeling spicy.

How many restarts, how often, how intense it is allowed to get. That's all configurable. You might be fine with the defaults but if you have something particular in mind you have a pretty solid set of knobs to twiddle.

By having Supervisors supervise Supervisors all the way back to the root Supervisor which sits on top of your particular Erlang application we have defined a supervision tree. The idea being that any issue out in a nice leaf node of a tree is probably addressable there and should not affect the rest of your nice stateful actor-packed system. But if it isn't, it is probably addressable at a branch closer to the tree.

A typical use-case for a Supervisor is a connection pool. And it might be the case that if any of the connections crash that indicates a problem with the server and you know from experience that you might as well re-establish every connection in the pool. Cool. Your Supervisor should do `one_for_all`. While if a phone-call crashes you probably don't want to dump out every call in the system just because one had a weird issue. `one_for_one` should do it.

This is where the Erlang "Let it Crash" idea comes from. It is a bit of a meme (often repeated, poorly examined, often misunderstood, sometimes with funny pictures). Fundamentally, if there is nothing useful your code can do on a failure, no mitigation, no meaningful fallback, then you might as well have it blow up and rely on the underlying tree to make whatever is most useful out of it. You should probably handle the usual suspects "oh, data is missing, let's 404". That is an expected failure mode. "oh, hard drive has imploded and disk can't be read" is a very different error and a 500 Internal Server Error is probably fine.

I don't think I've seen an up-and-running Elixir application go down due to exhausting the Supervision tree with erroring out. I have seen it many times as a failure mode for a misconfigured deploy, typically missing an env variable, where starting the initial supervision tree fails.

Supervision is the fundamental building block in Erlang's resilience story.

How is it used in Elixir? Not much. As in, we don't typically need to think about it. Phoenix ships a practical and helpful Supervision tree. Ecto ships a good Supervision tree for your database connections and all that. These Supervision trees live a peaceful secretive life under the guise of library code and you get all the advantage with none of the work. And the moment you start coloring outside the lines and building your own GenServers the facilities are there for you.

With your regular generated Phoenix web application all you see is that your `application.ex` file contains a few entries for different purposes. These are the first branches of your supervision tree and all that you really see.

You get `MyApp.Repo` for your Ecto Repo which is the abstraction for all your database connectivity, hiding your connection pools and whatnot. You get `MyApp.Endpoint` which wraps up your HTTP server (Cowboy or Bandit) in Plug and Phoenix abstractions to respond. Under this you will build up a subtree with a variety of jobs. For HTTP requests you have processes that respond. I believe Cowboy uses a pool and Bandit spawns more ad-hoc. There are also the WebSockets and the specific Phoenix Channels or LiveView processes that belong to the state of your application. This all branches out from your Endpoint as it is all related to your web serving.

You can hear more about [how Bandit operates](https://www.beamrad.io/53) on the BEAM Radio episode we did with Mat Trudel. We got into the process usage a decent bit there.

Separately you'll have `MyApp.PubSub` which starts the Process Groups (pg2) or Redis adapter for doing cool PubSub stuff. This is not strictly web-related, so it has a separate sub-tree. Someone did a thinking.

I appreciate that Phoenix doesn't try to hide this stuff from you any more than abstracting away the details. If you want to run your app without the web part you can quickly get to the idea of dropping the Endpoint from the list. You simply don't start it. Same with the DB, just cut out the Repo. Likewise if you need multiple DBs or multiple web services. You can make more Repos and Endpoints. It is explicit. The code is right there. It uses the standard Erlang and Elixir way of starting things.

Anyway, that's a detour from resilience and reliability. Fundamentally you have these supervisors, strategies as a structure to contain the blast radius of errors. The places this approach falls down is if you build a native implemented function (NIF) in C or similar. A crash in that brings down the whole VM. This is why doing work in properly managed BEAM code is preferred in most cases and why you'll often see attempts to re-implement things in Elixir/Erlang rather than just binding to an existing C library. Rust and Rustler, Zig and Zigler both offer NIF implementations with some additional safety. Of course they don't entirely remove the risks of native code though.

Rustler uses [dirty schedulers](https://medium.com/@jlouis666/erlang-dirty-scheduler-overhead-6e1219dcc7) and I expect Zigler does something similar or at least supports the old and the new options for building NIFs. So they should play nice-ish with Erlang scheduling. But fundamentally native code doesn't quite offer the same safety or same behavior.

Beyond Supervision trees there are more aspects of Erlang that are resilient. Scheduling and pre-empting was intended to produce [consistently low latencies](/unpacking-elixir-realtime-latency.html) but it also protects you from heavy workloads bogging things down, it prevents infinitely looping bugs from slowing the system to a crawl and in general makes things resilient to things not being ideal all the time.

Another thing is the Erlang heart. That's a module that can be used to detect the Erlang application itself going down and being able to start it again. An almost-supervisor for your entire application. The IoT [Nerves project](https://nerves-project.org/) also uses a variant of this to help ensure a resilient device.

Having built in other web frameworks and languages this is all a bit foreign. It is more reminiscent of building a microservice architecture than building a single application. But it does occur at the application level. It gives you tools to think about bringing up your system, bringing down your system, handling failure in your system. Those concerns all exist in other languages but the control-mechanisms tend to be quite coarse. Erlang and Elixir helps you think about how you design your application.

---

Do you have lessons learned that you'd like to share about building for resiliency? Does the Erlang way of doing it resonate with you? Let me know over email at {{< lars_email >}} or via {{< lars_twitter >}}.


