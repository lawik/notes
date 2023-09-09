The nine nines. 99.9999999% of uptime. Whether the AXD301 actually deserves to be held up as a system of nine nines seems debatable. I am not particularly interested in that debate. Erlang has a strong record for reliability and a design intended to help you as a developer and operator achieve your nines. Maybe just five of them. Up to you really.

Previous episodes of this article series has unpacked [Concurrency](/unpacking-elixir-concurrency.html), [Real-time & Latency](/unpacking-elixir-realtime-latency.html) and [the syntax](/unpacking-elixir-syntax.html). In this one we talk about how Elixir and Erlang achieve reliability.

There are agressive, defensive and rigorous practices you can adopt in a variety of ways to achieve reliability and resilience. Testing, profiling, specifications, design, observability, culture, operations and so on. This is not about those. This is about software abstractions that serve you in making your system more resilient and allow you to construct resilient designs.

Erlang implements the Actor model, the typical actor is the GenServer. The generic server. It is a process that has some nice facilities for receiving calls ("synchronous" messages, expecting a response within a timeout) and casts (asynchronous messages, with no expectation of reply). A GenServer is a bunch of API wrapped around the basic Erlang process. A process can receive and send messages. A GenServer (also other related types, gen_event, gen_statem) implements capturing errors that occur and the subsequent exit and passing information about the problems to the parental process as a message.

So what is the parental process. Well it is usually a special type of Erlang process called a Supervisor. A supervisor can run multiple child processes that implement the correct API and handle problems by applying a strategy.

The strategies are normally `one_for_one`, `one_for_all` and `one_for_rest`. One for one indicates that if a child crashes it, and only it should be considered when restarting. Leave the others alone. One for all means that the supervisor should restart all children if one child fails. This is often useful if the children are sharing a workload in some important way where restarting one would cause trouble with the others. One for rest is very nuanced in that the Supervisor will restart any child started after the crashing one. This can be used to kill a supporting process if the main one dies for example.

So a Supervisor supervises a set of child processes. This set can be dynamic, growing and shrinking as the system operates. If a single crash happens within a child it will apply the strategy. If the new child crashes immediately it will try again. If there are enough failures within a given threshold the Supervisor will consider itself failing at the job and crash.

This crashing is a feature, not a bug. By crashing it will propagate a signal whatever Supervisor started this Supervisor that there is an issue. This Supervisor can in turn apply strategies to try and recover the Supervisor through the tried and true practice of turning it off and on again.

How many restarts, how often, how intense it is allowed to get. That's all configurable. You might be fine with the defaults but if you have something particular in mind you have a pretty solid set of knobs to twiddle.

By having Supervisors supervise Supervisors all the way back to the root Supervisor which sits on top of your particular Erlang application we have defined a supervision tree. The idea being that any issue out in a nice leaf node of a tree is probably addressable there and should not affect the rest of your nice stateful actor-packed system. But if it isn't, it is probably addressable at a branch closer to the tree.

A typical use-case for a Supervisor is a connection pool. And it might be the case that if any of the connections crash that indicates a problem with the server and you know from experience that you might as well re-establish every connection in the pool. Cool. Your supervisor should do `one_for_all`. While if a phone-call crashes you probably don't want to dump out every call in the system just because on had a weird issue. `one_for_one` should do it.

This is where the Erlang "Let it Crash" idea comes from. It is a bit of a meme but fundamentally, if there is nothing useful your code can do on a failure, no mitigation, no meaningful fallback, then you might as well have it blow up and rely on the underlying tree to make whatever is most useful out of it. You should probably handle the usual suspects "oh, data is missing, let's 404". That is an expected failure mode. "oh, hard drive has imploded and disk can't be read" is a very different error and a 500 Internal Server Error is probably fine.

I don't think I've seen an up-and-running Elixir application go down due to exhausting the Supervision tree with erroring out. I have seen it many times as a failure mode for a misconfigured deploy, typically missing an env variable, where starting the initial supervision tree fails.

Another step in this is the Erlang heart. That's a module that can be used to detect the Erlang application itself going down and being able to start it again. An almost-supervisor for your entire application.

Supervision is the fundamental building block in Erlang's resilience story.

How is it used in Elixir? Not much. As in, we don't typically need to think about it. Phoenix ships a practical and helpful Supervision tree. Ecto ships a good Supervision tree for your database connections and all that. These Supervision trees live a peaceful secretive life under the guise of library code and you get all the advantage with none of the work. And the moment you start coloring outside the lines and building your own GenServers the facilities are there for you.

