Planned topics:

- Concurrency
- Syntax
- Realtime & Latency
- Resilience
- Observability
- Actors
- Web framework
- IoT & Embedded
- Leading edge (web, LiveView)
- Experimental (ML/AI)
- Reworking (type system)

---

Elixir is the thing I do most of my public writing and speaking about. It is my default programming language for the last 5-6 years. It suits my brain. Performs well for the kind of work I typically do. And usi very few drawbacks. Rather than writing yet another wide post trying to summarize what I think is beneficial about the language I want to try and go a bit deeper on one particular aspect I like. I hope this will be a series of stand-alone posts. We will see. This time we are unpacking Elixir concurrency (and parallelism).

I came up with PHP and then transitioned to Python. In PHP the concurrency model is pretty much whatever your web server (mod_php) and possible app server (php-fpm?) decides it should be. PHP generally assumes a shared-nothing model and each request is clear of whatever work is done in the others. In this regard they can run concurrently but they can also not share anything. Python .. it depends. But it is really bad at concurrency. The best solution seems to be just running more interpreters or using Python multiprocess or whatever it was called as threads and gevent are all subject to the GIL. I liked Python. I didn't enjoy trying to do performant concurrency in Python.

Elixir builds on Erlang. Fundamentally everything I'm writing about here is thanks to the Erlang Virtual Machine, aka. the BEAM, which has this stuff at its core. I will write in the context of Elixir. The things that are great here are also great about Erlang. Except possible the Task module, which is kind of neat and only in Elixir.

The BEAM is made to run a ton of lightweight processes. A type of green thread if you will. They take very little time to spawn, they take very little memory. A system can have millions of them. And if you many several millions of them you can bump the default limit. They are fundamentally a different thing from OS processes and threads. The BEAM itself is scheduled by the OS but on startup the BEAM creates threads for its schedulers. One per available CPU core by default. These in turn take care of scheduling processes. This is the source of concurrency and parallelism.

We won't go into detail on the Actor model and how that works here but that is built on top of the fundamental primitive of a process. A process runs a function, the function only has access to its own local variables and whatever arguments were passed in. This is Functional Programming so everything is immutable. A process cannot change the values in another function. This function running in a process has two cool special bits of API it can use. It can receive messages and it can send messages. Function runs to completion? Process goes bye-bye. Process runs in an eternal loop checking for messages and only acts if a message comes in? Now we end up talking about the Actor model which I said we won't do. So lets move on.

A process runs on one of the schedulers. If many processes taking a long time creates a queue on one scheduler the other schedulers can start stealing work to balance it out. If a particular process goes through a lot of function calls (internally called reductions), say 4000 for example, without completing then the scheduler will boot it, mid-run, to the end of the line. This is called pre-emptive multitasking and it is what Linux does to the OS processes. In the BEAM nothing gets to hold up the line for very long. This is how it ensures low latency which is another topic.

The immutable FP nature of Elixir and Erlang are pragmatic chocies to achieve a bunch of goals. It predates multi-core machines by a fair bit and they were targeting a different kind of concurrency, distribute systems made up of multiple machines, a cluster. The end result was accidentally convenient for achieving incredibly concurrency and parallelism on multi-core machines. Processes do not share state, they can only communicate asynchronously through message passing. This ensures it is safe to run them at the same time. Concurrent, executing the tasks during the same period of time and parallel, executing the tasks at the same time. All APIs that touch files, stdin/stdout and such have mechanisms for ensuring that contention is under control. The Erlang folks have spent a lot of time getting this right.

When I write my code I generally don't need to care about this very much. Most web requests take place inside a single process. They might communicate on through a database connection pool which involves more processes but overall I don't need to care about the underlying primitives. But I don't need to think about the Node.js way of racing to IO. CPU-bound work is fine. It won't disrupt the ability of other processes to get work done, both because multiple cores can do the work but also because if I spend many CPU cycles I will be pre-empted and other work can slip by. This is all handled by the web server I use, more than by me.

If I do want to do concurrent work there is the usual async/await mechanisms, implemented in the Task module. Not as weird as in JS. They just return a Task reference which is a helpful abstraction on top of the Process ID or PID. Then you can await on it to get a result or await on multiple to get multiple results. There is also a wicked function called Task.async_stream which will take an enumerable (list, map or similar) and run a Task for each entry as a lazy stream. By default it has a concurrency matching the number of available cores (just like the schedulers). This is essentially a fancy shortcut for using all your machine has to offer in the service of getting the work done for tasks that are embarassingly concurrent. It is very fun to use when bodging together scripts that you want to go fast. If the bottle-neck is IO it is usually not worthwhile.

Compared to PHP I have many more tools for interacting across the work that happens in my system. Process registries, PubSub mechanisms for message passing, memory-based shared state accessed through safe mechanisms (ETS for example).

Compared to Python I can do multiple things at once in simple and useful ways. In a language that is as expressive and as dynamic.

Compared to Javascript I can do multiple things at once easily without worrying about CPU-bound work holding up other requests and such. I get actual immutability instead of working really hard to pretend I do.

These comparisons are of course based on my understandings and experiences with the languages. I've stuck with languages I have some experience with but I don't hang out with them on a regular basis currently.

In summary; great options for concurrency and parallelism are not an afterthought in Elixir and Erlang. They are built into the very foundation of the languages and especially the runtime. And this matters for performance and practicality. Doing concurrency on the BEAM is not punishing, it is not risky. It is just what you end up doing by using it.