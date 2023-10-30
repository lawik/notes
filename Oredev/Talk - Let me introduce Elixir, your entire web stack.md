

COLD OPEN.

I am here to tell you about a stack that has the productivity- and developer-focus of Ruby and Rails. **In contrast** to Ruby it is quite performant. It brings concurrency and parallellism that blows Node JS out of the water. It is a high-level language working at an expressive abstraction level, in stark contrast to say Rust or Go. In the ecosystem we have an opinionated web framework with great performance and productivity, a darling of the startup world. It brings a novel and complete database layer that leaves ORMs in the rearview. We also have a clean and friendly frontend web UI solution that eliminates most Javascript and makes React jealous with actual immutability. Legendary reliability and unique runtime capabilities are a part of the deal.

It is your completely normal web dev but better at every step. This is what reimagining your foundation can do for you. This is what a community and ecosystem can do.

Built on a almost 40 year old foundation of Swedish innovation it has deep roots and high branches. Used by unicorns, gaming giants and telecom for its scalability, performance and reliability. Used by startups and indie hackers for its velocity and maintainability.

Oh. And it makes it trivial to do realtime, use audio and video, run machine learning inference and it simplifies your infrastructure. There is also a great IoT framework.

The most recent Stack Overflow survey says that the developers working in the language are among the best paid and most satisfied.

I'm Lars Wikman. I run a site and company called Underjord.io. Let me tell you about a language called Elixir.

TRANSITION; BREATHER

I just made a ton of claims and I want to address them all in turn to underline that I'm not actually blowing smoke or throwing unwarranted hype around. All the hype is warranted. A good first step might be to share my background.

Self-taught web dev.
Through Perl, PHP, JavaScript and Python I got into Elixir.
Done twenty years of programming.
I've worked professionally with Elixir for four years, used it for six.
I have learned it, used it, taught it, recruited for it and contributed to it in various way.

It is not the most well-known or widely used language. But it is a much appreciated and growing contender both according to what I've seen and according to surveys.

It is fine to take everything about me and what I say with a grain of salt. I'm an enthusiastic person. If you feel skeptical. Thats fine.

## The Demo

First step. Let me show you something.

(remix of ElixirConf demo)

- Let's just show what I'm saying on the page.
- Kind of neat. This is all a web app listening to my microphone.
- Very little JavaScript, enough to open the mic and ship samples.
- The rest is Elixir code driving the UI.
- The media processing is all written in Elixir. I never need to think about ffmpeg. 
- And I can get pretty detailed information about the media stream. I can ask it to please enable the waveform. Or even the fancy waveform.
- The machine learning inference is all done from Elixir. Not a trace of Python in this project.
- Every part of this holds to the same Actor model abstractions. The web UI, the steps in the media processing pipeline, the machine learning model. There are no surprises.

Alright. Lets unpack some of those claims I made.

## The runtime, Erlang & the BEAM

When building a programming language runtime you make design choices that enshrine trade-offs.

- Ruby & Python, high-level, elegant languages, dynamic, mutable and very flexible. Dog slow, terrible concurrency.
- Java & .Net, everything plus the kitchen sink. General, generic, safe. Okay everywhere, exceptional nowhere. Fast, due to immense investment more than fundamental design.
- Javascript's V8, high single-thread performance, cooperative multi-threading, fast if IO-bound. Very sensitive to CPU-bound work. Fast, due to immense investment in overcoming the limitations of an absurdly rushed design.

Elixir is built on Erlang and the BEAM Virtual Machine.

Erlang, the Ericsson language or more truthfully named after the mathematician, was built to be a high-level, opinionated language for solving hard problems in the telecom space. The requirements were, concurrency, consistently low latency, high availability, soft realtime, hot code updates to running systems, reliability and robustness. Those are some ambitious goals.

Erlang ended up implementing something close to The Actor Model as a way to provide a high-level structured paradigm to organize your system.

Every attempt to make a good concurrency API ends up avoiding shared state and communicating through message passing. Everything else ends up struggling with locks of some sort. Erlang is a functional language with immutability, which ensures that state cannot be shared at the language level. Erlang implements isolated processes as a unit of concurrent execution as well as message passing. These are language primitives.

Phenomenal multicore support with parallelism came later as a consequence of designing for concurrency and distributed execution. These are fundamentals of Erlang. The choices made in the initial design provide immense capabilities at runtime. It doesn't end just because you app is in production.

The BEAM virtual machine has proven itself over and over again, at Ericsson, Whatsapp, Discord, Blizzard, Riot Games, AWS and many, many more. It can run millions of lightweight processes and easily distribute workloads across a global cluster.

Erlang has been a cool niche language and the secret sauce of many impressive products and projects. It never achieved wide public popularity.

The BEAM VM is performant, by design, without the massive investment of the JVM, .Net or V8. It made trade-offs optimized for building services. And now our entire world is .. services. The web is services.

## The language, Elixir

Built by a Rubyist, José Valim, looking for something more performant. Elixir is very reminiscent of Ruby at the surface level. One layer deeper than syntax you quickly find it is a high-level, dynamic, **functional** programming language. Precisely like Erlang, semantically equivalent, but more approachable.

```elixir

@doc """
Take the popular combination of a first name and a last name. Produces a full name in the typical style.

### Examples

  iex> to_fullname("Lars ", "Wikman")
  "Lars Wikman"
  iex> to_fullname("Lars", "Wikman", true)
  "LARS WIKMAN"

"""
def to_fullname(firstname, lastname, airport_mode? \\ false) do
	[firstname, lastname]
	|> Enum.map(fn name ->
		name
		|> String.trim()
		|> then(fn name ->
		  if airport_mode? do
			  String.upcase(name)
		  else
			  String.capitalize(name)
		  end
		end)
	end)		
	|> Enum.join(" ")
end

```

## The ecosystem of Elixir

As a modern language Elixir has great package management and sweet tooling.

The mix command-line tool does project generation, builds, dependency management, task running and almost everything else.

The iex shell is a very full-featured REPL that is also incredibly useful when troubleshooting production systems.

The hex.pm package repository and hexdocs documentation are best in class.

Elixir brings ExUnit, a comprehensive and very fast testing framework, as well as ExDoc for the documentation tooling.

There is an established Language Server that will work with your regular editors and a new, faster and more reliable one currently being developed.

## Actor model, supervision trees & more

We have no time to cover the fundamentals of how Erlang achieves reliability. Suffice to say it has a very significant and well-respected legacy in that regard.

The debate is whether the nine nines are really a truthful representation of Ericsson's AXD301 and its reliability or a mere 5 nines is more accurate. You know a wretched 5.2 minutes of downtime per year.

If you want to understand the resiliency model, look up Supervision trees for a start. Elixir inherits this foundation and uses it for everything. It is a fundamental part of the language and runtime. And it limits the impact of errors in your application. I can also direct you to my Unpacking Elixir blog series that covers a lot of this ground if you want to read up on it.

## Phoenix - The Web Framework

The Phoenix web framework was created by another rubyist. Chris McCord. He spent a ton of time in Ruby trying to build something that abstracted doing live and realtime web UI in a simple way. It was really slow and difficult.

He jumped at the opportunity of working with a more performant language that was still familiar.

They started with fundamentals so we essentially have an MVC framework that most web devs will find familiar. Thanks to Erlang it is highly concurrent and parallel. It has highly optimized templating which means server-side rendering is really fast. It also shines as an API service.

They quickly built an abstraction for doing more realtime things and exposing the Actor model more cleanly to the web browser. It operates over WebSockets and is called Phoenix Channels. At that time they ran a benchmarking effort pushing the WebSocket support to find the limits. The process is covered in a good blog post. They hit 2 million concurrent active websockets on a single beefy box and called it good.

In a recent move for simplicity there is no longer any Node or NPM by default. Instead Phoenix ships Esbuild and Tailwind CSS as standalone binaries managed by Elixir libraries. You can change this fairly easily as well, nothing is too deeply integrated.

All in all a fundamentally very strong web framework. This is just the beginning.

Time to get disruptive.

## Phoenix LiveView

Imagine a full stack paradigm which covers 95% of use-cases by putting the server first and pushing the Actor model all the way to where it can smell the browser. With real functional programming, actual immutability, highly optimized templates and diffing to minimize data over the wire you get a fully interactive web app while only writing Elixir code. That's Phoenix LiveView.

It is a simple design that covers immense ground. A LiveView marries a small Javascript library, a WebSocket and a server-side Actor that holds state. This allows us to leverage the runtime's immense capabilities with managing state and operating concurrently. We retain near-realtime latency. It spares us from maintaining layers of API and building out a full-fledged frontend app in some other framework.

This is time to market. This is good beating out perfect. This is velocity and shipping. Keep It Simple Stupid. This is doing less. Leveraging a powerful runtime and well-designed frameworks.

But the devil is in the details right?
It is not for offline-first. It is not for super-intense interactivity.
But you can mix it with other approaches as well.

And it grows beyond the prototype. It has components, slots and all the things you would expect from a frontend framework. It has a file upload implementation to die for.

Even being still pre-1.0 it is seeing heavy use throughout the community.

And if you use it you don't have to write Javascript. But you can. There are escape hatches and integration points for what you need to get done.

This ecosystem is pragmatic that way.

## Builder stack

This is a web stack by builders for builders. I've been to ElixirConf. It is dominated by people who use the language and framework to get stuff done.

Erlang is an industry language not an academic one. Ruby has always been a tool for creation. Elixir is a synthesis of those cultures and the software they built.

Functional Programming without pretention. No monads required. It is a workhorse replacement for your PHP, your Ruby, your Python, your Node.js that can do multiple things at the same time. And it offers a web framework which gives you as much abstraction as it can with minimal weird magic. Simply building on top of the powerful abstractions that escaped Ericsson in 1998. And some macros.

Building with Elixir you join a community that has spawned, grown and progressed without a megacorp at the helm. An ecosystem that is stable and where code rarely churns or changes. A language that was considered largely complete several years ago. The language is mostly being polished at this point. Meanwhile the ecosystem grows around it.

This is how you make something fundamentally different.

## Getting fancy: Media

For example. Most web developers end up converting videos to web formats at some point. Almost everyone will use ffmpeg for that. They should. It is very capable. You create a worker on a queue, you shell out, wait for the command to complete and do something with the output. Or you start one for a live session of some sort and hope to the gods above it does not crash.

Managing a complex ffmpeg pipeline is not particularly fun, you have limited control, poor insight into the process and orchestrating it well is hard.

In Elixir we use Membrane. For some workloads it will bind to ffmpeg, or portaudio or whatever it needs. The flow of processing and many of the actual processing steps are done entirely inside Elixir.

I've done media processing in Python and PHP. It was never this powerful or easy. They have not built a set of ffmpeg bindings and called it good. They've built a framework for media processing with a focus on robust live streaming. Completely in tune with the historic strengths of Erlang. It is a design which tackles the difficult challenge first. The rest falls into place.

They tackled live streaming and they solved regular offline transcoding as a by-product.

## Getting crafty: Machine Learning

Another example. The Bumblebee project builds on the Nx project. Nx stands for Numerical Elixir and is an audacious act of tackling a thing Elixir should be terrible at. Number crunching. It builds on other tools, Google's XLA libraries, TorchX and other accelerators to enable us to write numerical calculations in Elixir that are then run in an accelerated fashion. This means that Machine Learning can be ported to Elixir. Nx is a building block to orchestrate number crunching and write it with high-level code.

Bumblebee is the project that lets completely mathless web devs like myself casually bring ML models into my day-to-day work.

Simple models for sentiment analysis of text run trivially on CPU. The Whisper speech-to-text model can also run quite well that way. If you have a GPU you can do a lot more with models like Stable Diffusion and some LLMs that have been ported. Recently, Llama 2 became available as well as Mistral. The Bumblebee abstraction level makes it trivial to use in your app.

While Python leans on C++ libraries like Ray for multi-node orchestration of GPU hardware. In the end it actually implements something like the Actor model. Nx already has that. Erlang made it easy.
Every ML/AI venture is also trying to ensure efficient batching of GPU loads. Nx already does it.

So you have a tool that can efficiently use multiple nodes with multiple GPUs and apply batching to maximise utilization. Out of the box.

Most data shops use code notebooks to develop their stuff but they barely reproduce, can't be shipped and are severely limited. Elixir has Livebook which is fully collaborative, text-based, straightforward and actually useful beyond data. Many devs use it for operations scripting, running it against their production nodes. And you can use it to ship applications. And with immutability you get almost perfect reproducability.

Bumblebee and Nx are not just ways to run some Python code that runs your ML models for you. They are an ambitious stab at the weakest part of the stack and it has already enabled a ton of practical use. It slots right into the toolset. And the ecosystem is growing around it.

How is this web? Well, I don't know about you but my clients talk a lot about AI right now. And this type of deep stack development, where full stack is not enough and we have to go off into data and ML is only getting more common.

## Getting hands-on: Embedded Linux

How about hardware? The IoT framework Nerves is appropriate for most jobs where an embedded Linux is appropriate. Not microcontrollers, rather single-board computers and the like.

It treats Linux as a thin substrate and brings up the BEAM VM as the primary operating system. Elixir sets up your networking. Elixir talks to your custom hardware. Elixir checks for updates and pulls down binary diffs of payloads. Elixir does the blue/green partition switch.

Instead of writing a sensitive piece of hardware in a language that is finicky and hard to get right, like C. You write it in a language known for resilience and reliability. You get granular error handling and fault recovery. And you get to write your product in a high-level language without sacrificing too much in performance. Without losing control of your hardware.

You also gain a lot in observability and debuggability.

Nerves is a pretty tight bundle. If you have a Raspberry Pi in a drawer somewhere, I suggest you give it a try. My quickstart video for getting Nerves going was 1 minute and about 40 seconds long. It is not hard.

I should also mention NervesHub which is a tool for managing a fleet of IoT devices deployed in the wild. Shipping updates to firmware, troubleshooting, reverse console and all that.

And if you need something low-level Erlang has escape hatches into C called NIFs. The community has since built that out to also support Rust and Zig through libraries. You have options.

## Deployment

Most people deploy Elixir the way they deploy everything else. In a Docker container. You can also compile a release which is essentially an archive that contains everything required to run your application. A kind of complicated variant of a static binary. Why is it complicated?

Because Erlang and Elixir support hot code updates. This facility is not commonly used but if you want to hear about people who do use it I suggest watching Erlang talks from Whatsapp or listen to BEAM Radio episode 12 with Bryan Hunter, titled Punking the Servers. Fundamentally this allows us to update the code of our system without ever bringing it down.

It also powers the Phoenix dev server to do hot updates.

Elixir is often deployed with clustering, commonly called Erlang Distribution, which means all nodes are connected to each other and can exchange messages. This enables a lot of cool stuff without needing separate infrastructure for coordination. An Elixir app usually does not need Redis or RabbitMQ for coordination or caching.

So we deploy just like everyone else. But also better. And with more possibilities. The ceiling of potential is very high.

## Observability & introspection

While Elixir is a good citizen of the world and supports Open Telemetry and has some really nice regular tools around logging, metrics and traces it also does not stop there. The BEAM VM allows us to do much, much more. A default Phoenix install brings in Live Dashboard which allows you to look at a bunch of statistics about your application at runtime as well as delve into process resource usage and much more.

Erlang has tools for introspecting and manipulating a running system and all of those tools are available to us. This is what a real high-level of abstraction should look like.

Did you introduce a memory leak? Alright, just sort your processes by memory usage and see which one it is. Everything runs in a process. Has to be one of them.

Want to trace function calls in your system live and inspect what your code is seeing as the calls come in? Sure, that's available. There are even high-level tools like the Orion web UI that let you run distributed traces to profile particular functions and graph the performance numbers. No up front instrumentation required.

What can you pull from your system at run-time? What can you do to figure out why, at run-time? Without redeploying?

## Your whole stack

Elixir and Erlang have an unusual ability to absorb work that is usually handled by external services. Caching, queues, workers, publish-subscribe messaging, clustering can all be handled in the application. Generally we don't need Redis or Nginx.

Heck, Erlang ships with a full-fledged distributed database that is very capable called mnesia. We still mostly use Postgres though.

Phoenix LiveView continues that by taking a big bite out of the web frontend. Suddenly whole categories of apps can be self-contained without making any UX trade-offs.

You application can perform multiple tasks without them disrupting each other. Your application is a whole system.
## A Future built four decades ago

When you line up all the unique upsides of the BEAM, Erlang and Elixir it ends up sounding too good to be true. It sounds a bit futuristic. I would expect someone to say that they are still in the early days of this roadmap and that they would love to have us along for the journey as they ship their developer preview. It sounds like unfounded hype that will never come to pass.

Fortunately for us someone decided to build something futuristic almost four decades ago. And then Elixir put a spotlight on it about one decade ago. You can just get started. It exists. I use it every day.

As William Gibson said: the future is already here, it is just not evenly distributed yet.

30:29