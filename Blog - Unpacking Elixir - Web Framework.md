
In this series I've been unpacking various facets of [Elixir](https://elixir-lang.org/). Mostly this has meant trying to explain Erlang and the BEAM through the lens of Elixir. Now we are moving into the domain of the web framework. This is where I dare say that Elixir has much more to say than Erlang. Erlang has to my understanding never landed fully on a canonical preferred web framework. Elixir has [Phoenix](https://www.phoenixframework.org/) and this post will be unpacking Phoenix. The Elixir web framework.

As for Erlang [this Awesome Erlang list](https://github.com/uhub/awesome-erlang) has a ton of web frameworks. There have been many but I have never detected a consensus on what one "should" use. Actually, when I spoke to [Robert Virding](https://www.wikidata.org/wiki/Q107596747) over beers at a conference I asked something about this and he more or less said that Elixir and Phoenix should be the preferred web framework for the BEAM. The exact question and the exact answer are muddled by time and memory. My understanding is that he much prefers Erlang for everything else but really wishes that people would just use LfE ;)

This should not be taken as a criticism of Erlang, rather a kudos to Elixir for establishing and maintaining this useful cohesion. The Elixir ecosystem has always had a fair bit of focus on the web in a way which Erlang has not.

People have built other web frameworks in Elixir. Phoenix remains the major player. It is the Rails/Django/Laravel of the Elixir ecosystem though I would argue it is more lightweight. In good ways and possibly bad.

## Plug

We shouldn't start on Phoenix. That's the high-level framework. The fundamentals of dealing with web requests and creating responses are handed to [Plug](https://hexdocs.pm/plug/readme.html). Plug deals with headers, bodies, query params, URLs, paths and all of that by providing the concept of composable plugs. A very simple plug:

```elixir
defmodule MyApp.NotFoundPlug do
	alias Plug.Conn
	
	def init(opts) do
		opts
	end

	def call(conn, opts) do
		conn
		# Send a 404 to the client
		|> Conn.send_response(404, "Not found")
		# Do no further processing of this plug pipeline
		|> Conn.halt()
	end
end
```

Plug also provides a module for routing and building up routes based on path and method. This example is from the docs.

```elixir
defmodule MyRouter do
  use Plug.Router

  plug :match
  plug :dispatch

  get "/hello" do
    send_resp(conn, 200, "world")
  end

  forward "/users", to: UsersRouter

  match _ do
    send_resp(conn, 404, "oops")
  end
end
```

A plug which does not halt continues to the next plug specified. This means every step of authentication, authorization, parsing and so on can be layered. Similar to a middleware approach. They all work with the `Plug.Conn` struct.

Plug is a rather elegant way of getting web stuff done. Good building blocks. I stole some code from Frank Hunleth and Connor Rigby of the Nerves project when I wanted to do [my live visitor counter in an image](https://underjord.io/live-server-push-without-js.html) (also [the Plug](https://github.com/lawik/mjpeg/blob/master/lib/mjpeg.ex) and [the project](https://github.com/lawik/mjpeg_example)) to do chunking and produce an MJPEG. It didn't need the rest of Phoenix. A good case for straight Plug. But also, a Phoenix Controller is just a Plug and could do it just as well.

## Web servers

Historically Phoenix has leaned on a web server called [Cowboy](https://ninenines.eu/docs/en/cowboy/2.10/guide/) written in Erlang. It has been a very reliable workhorse for a long time and has done well in that role. It connects to Plug via the `plug_cowboy` library. Increasingly I see projects pick up [Bandit](https://hexdocs.pm/bandit/Bandit.html) which is intended to be a replacement written in Elixir. This allows the community a lower barrier to contribution as more people in the Elixir space know Elixir than Erlang. There is more to it as well. We covered some of that in [an episode of BEAM Radio](https://www.beamrad.io/53) if you are curious. Supposedly Bandit also benchmarks as a bit faster than Cowboy which is of course a nice perk.

Something these web servers have in common is that they are not your Ruby or Python application web servers. No reverse proxy required unless you want one. They can actually be trusted to do real frontline work. Erlang was built for it.

## Phoenix

Now I think we can tackle Phoenix. Ah yes, the "Rails of Elixir". But not nearly so similar. Now I don't have any significant experience with Rails but I talk to people who do. I have experience in Django. Both Django and Rails work hard to help the developer be productive for the common case. The Pareto Principle, 80/20 rule, all that. And beyond.

In Django this is achieved through "magic". Mostly inheritance of classes that introspect what you put in them and do things based off of that. They also take metaprogramming very literally and you end up putting a bunch of things in a class-within-a-class called Meta as I recall.

These are super-dynamic languages where monkey-patching and other fun stuff is incredibly available. This of course means you should limit how much you use this fun stuff as much as possible. A code-base without discipline can get very messy on top of these languages and frameworks.

Phoenix tries not to rely on "magic". We call them [macros](https://hexdocs.pm/elixir/macros.html) instead.

I kid. Macros are among the more confusing parts of Phoenix as well as Plug but they are generally there to manage some inherent complexity for you and they are much of the time still in "your" code.

Typically you start a Phoenix project using `mix phx.new my_app` which generates a project that you then own. Sure, you have dependencies, the code of which you don't own, but your MyAppWeb module has macros for bringing in the necessary functions for Controllers or LiveView and you can adapt that to your way.

I've heard multiple people go "that's a lot of files" when generating a Phoenix project and I agree, that's the impression you'll get. But most of the files have fairly clear purpose once you get to know them and they are there to make things explicit and hand you the reins instead of mysteriously and magically inheriting things at you. There are also hygiene things like gettext that you might not use in your first few projects that are there because they just ought to include it. And you'd be pissed if it was not there when you needed it.

### Opinionated design, or a lack thereof

We often talk about opinionated design in web frameworks. The reason is generally that an opinionated design makes significant trade-offs for some cases in order to support the common case. Pareto principle, 80/20 rule, all that, again. By providing an opinionated design you eliminate the need for many decisions and ideally provide well-proven good-enough solutions or at least helpful simplifications.

Phoenix is not deeply opinionated. I think . Erlang is incredibly opinionated at a fundamental level. It makes a ton of choices in the service of building services and Elixir inherited those opinions. We've traded off a number of things we don't care about to get a fantastic foundation for a web framework. We get trivial concurrency and parallelism but have traded off small binaries and number crunching. We have consistent latencies but don't get the speed of mutable state.

I started out considering Phoenix as an opinionated framework in the vein of Django. I don't know what gave me that idea aside from them both being web frameworks. Sure, it brings in some opinions such as "specifying routers in a central place is nice" and "this is how you should bring in your helper functions for doing controllers". It also abstracts away connection pools and supervision trees and there are opinions enshrined there but that's usually not what people mean when talking about opinionated framework designs.

There are some more opinionated parts outside the core of Phoenix. Channels is an opinion on top of WebSockets, Presence as well. Phoenix LiveView is heavily opinionated and makes fairly aggressive trade-offs for great wins in productivity.

Phoenix also brings in Ecto by default and Ecto is a fairly opinionated approach to relational databases. Changesets and the query DSL are both quite flexible but they push you towards a particular way of working that the library believes is best.

The most contentious part of Phoenix seems to be Contexts. Which is the connective tissue between a Controller, Channel or LiveView and the Ecto-driven data layer. There is an approach generated by default but it is much discussed and will generally require you to apply your own designs and ideas. It it very open ended. There is no special Context code. There is an opinion coming from the Phoenix generators but it seems very softly held and it is not really in the bones of the framework.

Overall I think Phoenix has been layered well and I find this restraint of opinion in the base framework means that there isn't a big desire for a lightweight microframework alternative to Phoenix (see Flask, FastAPI in Python) because if you just ask it to not add a database or skip the HTML bits or whatever configuration you need you will get your minimal API service or you full-trim HTML-spitting machine.

## The Phoenix feature set

I will try to capture the things that make up Phoenix. Major features as it were.
### Project generation

Phoenix does a bit of inversion of ownership and as I've mentioned, produces a bunch of files that you can then own and evolve. I run `mix phx.new` on something like a weekly basis as I try a new hack of some sort. The generator has a bunch of options, choose database (postgres, mysql, sqlite) or skip Ecto and database details entirely. HTML or not. LiveView or not. Assets or not. This is the normal starting point.

There are other starter templates. Legendary, Petal Pro and some others. I can't vouch for them. I've worked on a Petal Pro project, it was fine, it certainly brought more opinion around templating and layouts.

### Ecto

The database layer. Not locked to Phoenix in any particular way but it does ship by default.

Ecto provides relational database functionality to your app and stops you from making a bunch of mistakes that could lead to SQL injection attacks and the like. This informs Ecto's design in unusual ways. It relies a decent bit on compile-time macros for building queries. There are also escape hatches pretty much wherever you might need them.

#### Repos (Ecto.Repo)
A repo is an abstraction over a supervision tree that manages database connection pooling. A typical app deals with one Repo named `MyApp.Repo` and it provides all the query functions and such.

If you are dealing with two separate databases you can easily set up and additional repo. And if you are dealing with multi-tenancy or some other multi-database situation you have "dynamic repos" functionality which will let you work that way as well.

#### Schemas (Ecto.Schema)

The Schema module exposes a DSL for specifying database tables, mostly. It can also be used to specify schemas that are not backed by a database for various purposes. But mainly, this correlates with Django or Rails models while being significantly less magical in nature. They boil down to Elixir Structs.

### Changesets (Ecto.Changeset)

A way to define validation rules for schemas (and other data). This will help you produce good errors, integrate actual errors returned from the database gracefully and many other things. Changesets are used in and around inserts and updates. They are a large topic and well worth reading up on because they are quite and interesting and useful design.

### Web stuff

#### The Endpoint (MyAppWeb.Endpoint)

This module is generated for you but when added to your supervision tree it starts a Phoenix Endpoint which contains a supervision tree for starting any Phoenix-owned process. Whether you use the Plug integration for Cowboy or Bandit it will set up your web server to listen for inbound requests and route them to Plug and the Endpoint. The Endpoint defines a bunch of fundamental plugs and config. It then typically defers to the Router for further handling of requests.

#### Router (MyAppWeb.Router)

The router module is where you use a combination of Phoenix and Plug plugs to handle requests and delegate them to Controllers and LiveViews. It is a nice central place for structuring this and also allows you to define pipelines for other plugs that need to be applied, such as checking authentication and authorization.

#### MyAppWeb

This holds your macros for controllers, channels and liveviews. These macros mostly bring in other Phoenix functionality but they are in your file and you can and should use them as an extension point for bringing in your own tools.

#### Controller (MyAppWeb.MyPageController)

A controller handles a request. The Controller might produce JSON API responses, HTML, file chunks or whatever else. Doesn't matter. If you are rendering HTML you get into templating and components. A controller is actually also just a Plug.

#### Templating (Heex)

Heex is an evolution of the regular Eex templating that ships with Elixir. Heex is HTML-aware and will tell you when you screw up your closing tags. It also has nice syntax for arguments beyond basic string interpolation and such.

```elixir
def render(assigns) do
	~H"""
		<div :for={thing <- @items} :if={thing.great?} class={@myclass}>
			<.custom_component thingie={thing} />
		</div>
	"""
```

### Innovations

I want to shine some light on the bits that I think offer capabilities beyond what we typically see in web frameworks or that do things better than most web frameworks.
#### PubSub

Functionality used as an underpinning for Phoenix Channels. PubSub is a publish/subscribe mechanism for loose coupling of communication across your application. It uses Erlang's process groups and Erlang distribution. Unless you are on Heroku in which case you need the Redis adapter. You should be clustering or Chris McCord will be upset with you.

Phoenix PubSub is incredibly practical for letting processes track a topic and receive messages when things happen on that. And as GenServers, Channels and LiveViews are processes that can handle messages you can use these for many niceties. A common one is informing a LiveView that content it is showing has in fact changed. It can then do whatever it considers appropriate to inform the user.

#### Presence

Built to support usage in Channels but usable as a more general tool. Phoenix Presence lets you track the ephemeral presence of things (usually users). Are they online? Busy? Away with a small message? Are they on mobile only? It uses a CRDT approach to minimize how much data it needs to keep around while creating an eventually consistent model of the world without requiring a separate storage backend.

#### Channels

An abstraction, intended to go on top of WebSockets though it will do long polling if necessary, it provides an abstraction for connecting to channels and joining rooms within them. Each WebSocket is backed by a GenServer on the server side and will let you keep state about the connected user. Typically you connect to it with a JavaScript client which handles failures, reconnects and provides some API niceties.

Fundamentally the most important bits are WebSockets and server-side actors. But the rest is nice too.

#### LiveView

The belle of the ball for most of us. The point of LiveView is to eliminate the need to write JavaScript for most web development tasks. It allows driving highly interactive web UI based on server state. This typically happens over a Channels-style WebSocket. You can annotate your Heex templates and components with attributes such as `phx-click` and similar to allow sending events to the server. The event is processed to update server state and a minimal diff is calculated and passed back to the browser which can then patch the DOM using morphdom.

You would have a very hard time getting payloads this lean using a SPA with your own API implementation. It also leans hard on an Erlangism: consistently low latencies.

There is also support for driving certain simpler JS/CSS updates without a server round-trip, animation support, streamed listings, functional stateless component, stateful components and more that I'm forgetting.

It is also trivial to push server-side changes to relevant LiveViews, thanks to the BEAM and Phoenix PubSub.

Here we have a very opinionated design. It sacrifices offline support for not needing to write as much code or maintain as many interfaces. It is an immense time-saver. There are many more posts about LiveView out there. We can leave it at this point.

### Asset pipeline

Once upon a time Phoenix shipped brunch I think. Then they switched to the industry standard: Webpack. Then they grew sick of the massive support burden it was to help people keep their node and npm setups working. Phoenix switched to Esbuild delivered through an Elixir library via Hex.pm.

I think this was a very good move. I have so many fewer asset problems now.

### Mailer

Phoenix ships with Swoosh which is an abstraction for email. It has plug-ins for many popular transactional email providers. So once you go into production you have an easy time doing password resets or magic links.

It also has a very nice little dev tool that lets you check a mailbox for the mail that has been "sent".

### Live Dashboard

Underpromoted cool thing. It is an observability dashboard web UI that you can ship by default in your admin and do simple things like:

- Investigate your running processes
- See breakdowns of memory usage
- See OS metrics
- See ETS table usage
- Capture request logs

And more. For no effort you get a practical first-look tool for investigating a misbehaving system. This is very Elixir. All the primitives and possibilities come from the BEAM and have been possible in Erlang since forever. But Elixir made it nice, simple and for most of us it is there by default (because Phoenix).

## Your app is not just "web"

Most systems have more duties than just serving web traffic. Often this is delegated to queues/brokers, workers, other services, Redis, databases, cloud functions or whatever else. A Phoenix app is a BEAM application first. You can run many other workloads in it. You are building a system and it doesn't have to be several applications under this runtime.

Importantly Phoenix does not infect your system in any particular weird way. It doesn't warp the application away from normal Elixir application conventions.

## Eliminating infrastructure

I have touched on this throughout. But fundamentally a Phoenix application is only really expected to need a backing relational database. Typically Postgres.

No Redis. No mail workers. No separate task-runners. It can all occur from your application.

## Wrapping up

Phoenix is very much the canonical web framework for Elixir and it has massive gravity in the community and ecosystem. I think we benefit a lot from that. There is a lot of common, directed effort centering on Phoenix and Ecto.

It is a very solid web framework, on top a legendary runtime. And you build inside it with a very approachable language.

We get the system design and development possibilities afforded by our runtime where we have very few things limiting us in what we want to do.

And then we get the innovations that have been built in a way that is essentially unique. Other ecosystems have copied LiveView, as they should. But their variants can't do everything that LiveView can and they will have challenges getting there if they try. And that disregards all the practicalities of making an application be live.