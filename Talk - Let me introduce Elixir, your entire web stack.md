- Cold open, the too-good-to-be-true pitch
- The language, Elixir
	- The Erlang runtime experience
		- Proven: Whatsapp, Ericsson, Cisco
	- The Ruby developer experience 
	- Massively concurrent, resilient
	- High level and dynamic
		- Dynamic with good reason (more on this later, runtime observability)
- The web framework, Phoenix
	- All the normal stuff, pretty much MVC
	- Performant, concurrent & parallel, very fast rendering
	- Scales extremely far (WebSocket experiment)
	- No Node or NPM by default, Esbuild and Tailwind included if wanted
- The new paradigm, LiveView
	- Full stack, server first
	- Highly interactive, realtime UI
	- Write little or no Javascript

COLD OPEN.

I am here to tell you about a stack that has the productivity- and developer-focus of Ruby with Rails. In contrast to Ruby it is quite performant. It brings trivial concurrency and parallellism that blows Node JS out of the water. It is a high-level language working at an expressive abstraction level. In the ecosystem we have an opinionated web framework with great performance and productivity, a darling of the startup world. A clear and productive database layer that prevents SQL injection at compile time. A clean and friendly frontend web UI solution that is similar to Vue JS and leaves React in the dust.

It is your completely normal web dev but better at every step.

Built on a almost 40 year old (TODO: Verify) foundation of Swedish innovation it has deep roots and high branches.

Oh. And it makes it trivial to do realtime, use audio and video, run machine learning inference and it simplifies your infrastructure. There is also a great IoT framework.

I'm Lars Wikman. I run a site and company called Underjord.io. Let me tell you about Elixir.

TRANSITION; BREATHER

I just made a ton of claims and I want to address them all in turn to underline that I'm not actually blowing smoke or throwing unwarranted hype around. A good first step is to share my credentials.

Self-taught web dev.
Through Perl, PHP, JavaScript and Python I got into Elixir.
I've worked professionally with Elixir for four years, used it for six.
I have learned it, used it, taught it, recruited for it and contributed to it in various way.

It is not the most well-known or widely used language. But it is a much appreciated and growing contender according to what I've seen.

It is fine to take everything about me and what I say with a pinch of salt. I'm an enthusiastic person. It's fine.

Aright. Lets back some claims.

## The runtime, Erlang & the BEAM

When building a programming language runtime you make design choices that enshrine trade-offs.

- Ruby & Python, high-level, elegant language, dynamic, mutable and very flexible. Dog slow, terrible concurrency.
- Java & .Net, everything plus the kitchen sink. General, generic, safe. Okay everywhere, exceptional nowhere.
- Javascript's V8, high single-thread performance, cooperative multi-threading, fast if IO-bound.

Erlang, the Ericsson language, was built to be a high-level, opinionated language for solving hard problems in the telecom space. The requirements were, consistently low latency, high availability, soft realtime, live code updates, reliability and robustness. Concurrency and parallellism came later as a consequence of designing for this. It also provides immense possibilities at runtime, in production.

The BEAM virtual machine has proven itself over and over again, at Ericsson, Whatsapp, Blizzard, Riot Games and many, many more. It can run millions of lightweight processes, distribute workloads across a global cluster.

Erlang has been a nice language and the secret sauce of many cool products and projects. It never achieved wide public popularity.

## The language, Elixir

Built by a Rubyist, José Valim, looking for something more performant. Elixir is very reminiscent to Ruby at the surface level. One layer deeper than syntax you quickly find it is a high-level, dynamic, functional programming language. Precisely like Erlang but a fair bit more approachable.

TODO: Optimize code sample

```elixir

@doc """
Take the popular combination of a first name and a last name. Produes a full name in the typical style.

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

As a modern language there is great package management and sweet tooling.

- Mix - Project generation, build tooling and more
- Hex - Dependency management, used through mix (TODO: IMAGES for hex.pm and hexdocs)
- ExUnit - Testing (TODO: IMAGE)
- ExDoc - Documentation & examples as doctests (TODO: IMAGE)

## Actor model, supervision trees & more

We have no time to cover the fundamentals of how Erlang achieves reliability. Suffice to say it has a very significant and well-respected legacy. Elixir inherits that foundation and uses it for everything. It is a fundamental part of the language and runtime.

## Phoenix - The Web Framework

The Phoenix web framework was built by another rubyist. Chris Mccord (TODO: spelling). He spent a ton of time in Ruby trying to build something that abstracted doing live and realtime web UI in a simple way. It was really slow and difficult.

He jumped at the opportunity of working with a more performant

- The web framework, Phoenix
	- All the normal stuff, pretty much MVC
	- Performant, concurrent & parallel, very fast rendering
	- Scales extremely far (WebSocket experiment)
	- No Node or NPM by default, Esbuild and Tailwind included if wanted
- The new paradigm, LiveView
	- Full stack, server first
	- Highly interactive, realtime UI
	- Write little or no Javascript