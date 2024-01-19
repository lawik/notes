- Hello Phoenix
- Well, actually Plug!
- Composable, reasonable Plug
- Web Servers
	- Not your grandpas web servers
		- Cowboy + Ranch
		- Bandit + Thousand Island
- But really, Phoenix
- Divergence from Rails/Django
	- Less magic
	- Some macros
	- More explicit
	- More annoying or clear, one of those
- Featureset
	- Project generator
	- Ecto
		- Repos
		- Schemas
		- Changesets
	- Fundamental Webbing
		- Endpoint
		- MyWeb module
		- Router
		- Controllers
		- Templates/Components
			- Heex, HTML-aware templates
	- Innovation
		- PubSub
		- Presence
		- Channels
		- LiveView
- BEAM things
	- Your app is not just web
	- Multiple Phoenixes
	- Other workloads
	- Less infrastructure

In this series I've been unpacking various facets of Elixir. Mostly this has meant trying to explain Erlang and the BEAM through the lens of Elixir. Now we are moving into the domain of the web framework. This is where I dare say that Elixir has much more to say than Erlang. Erlang has to my understanding never landed fully on a canonical preferred web framework. Elixir has Phoenix and this post will be unpacking Phoenix. The Elixir web framework.

As for Erlang [this Awesome Erlang list](https://github.com/uhub/awesome-erlang) has a ton of web frameworks. There have been many but I have never detected a consensus on what one "should" use. Actually, when I spoke to Robert Virding over beers at a conference I asked something about this and he more or less said that Elixir and Phoenix should be the preferred web framework for the BEAM. The exact question and the exact answer are muddled. This should not be taken as a criticism of Erlang, rather a kudos to Elixir for establishing and maintaining this useful cohesion. And the Elixir ecosystem has always had a fair bit of focus on the web in a way which Erlang has not.

People have built other web frameworks in Elixir. Phoenix remains the major player. It is the Rails/Django/Laravel of the Elixir ecosystem though I would argue it is more lightweight. In good ways and possibly bad.

## Plug

We shouldn't start on Phoenix. That's the high-level framework. The fundamentals of dealing with web requests and creating responses are handed to Plug. Plug deals with headers, bodies, query params, URLs, paths and all of that by providing the concept of composable plugs. A very simple plug:

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

Plug is a rather elegant way of getting web stuff done. Good building blocks. I stole some code from Frank Hunleth and Connor Rigby of the Nerves project when I wanted to do [my live visitor counter in an image](https://underjord.io/live-server-push-without-js.html) (also [the Plug](https://github.com/lawik/mjpeg/blob/master/lib/mjpeg.ex) and [the project](https://github.com/lawik/mjpeg_example)) to do chunking and produce an MJPEG. It didn't need the rest of Phoenix. A good case for straight Plug though to be fair a Phoenix Controller would do it as well.

## Web servers

Historically Phoenix has leaned on a web server called Cowboy written in Erlang. It has been a very reliable workhorse for a long time and has done well in that role. It connects to Plug via the `plug_cowboy` library. Increasingly I see projects pick up Bandit which is intended to be a replacement written in Elixir. This both allows the community a lower barrier to contribution as more people know Elixir than Erlang. It also has some nuanced effects on how development is done. I neither want to or have room to unpack that here. We covered some of that in [an episode of BEAM Radio](https://www.beamrad.io/53) if you are curious. Supposedly Bandit also benchmarks as a bit faster than Cowboy which is of course a nice perk.

Something these web servers have in common is that they are not your Ruby or Python application web servers. No reverse proxy required unless you want one. They can actually be trusted to do real frontline work. Erlang was built for it.

## Phoenix

Now I think we can tackle Phoenix. Ah yes, the "Rails of Elixir". But not nearly so similar. Now I don't have any significant experience with Rails but I talk to people who do. I have experience in Django. Both Django and Rails work hard to help the developer be productive for the common case. The Pareto Principle, 80/20 rule, all that. And beyond.

In Django this is achieved through "magic". Mostly inheritance of classes that introspect what you put in them and do things based off of that. There are super-dynamic languages where monkey-patching and other fun stuff is incredibly available. This of course means you should limit how much you use the fun stuff as much as possible. A code-base without discipline can get very messy on top of these languages and frameworks.

Phoenix tries not to rely on "magic". We call them macros instead. I kid. The macros are among the more confusing parts of Phoenix as well as Plug but they are generally there to manage some inherent complexity for you and they are much of the time still in "your" code.

Typically you start a Phoenix project using `mix phx.new` which generates a project that you then own. Sure, you have dependencies the code of which you don't own but your MyAppWeb module has macros for bringing in the necessary functions for Controllers or LiveView and you can adapt that to your way.

I've heard multiple people go "that's a lot of files" when generating a Phoenix project and I agree, that's the impression you'll get. But most of the files have fairly clear purpose once you get to know them and they are there to make things explicit and hand you the reins instead of mysteriously and magically inheriting things at you. There are also hygiene things like gettext that you might not use in your first few projects that are there because they just ought to. And you'd be pissed if they weren't when you need them.

### Opinionated design, or a lack thereof

We often talk about opinionated design in web frameworks. The reason is generally that an opinionated design makes significant trade-offs for some cases in order to support the common case. Pareto principle, 80/20 rule, all that stuff. By providing an opinionated design you eliminate the need for many decisions and ideally provide well-proven near-optimal solutions or at least helpful simplifications.

Phoenix is not deeply opinionated. It doesn't have to be. Erlang is incredibly opinionated at a fundamental level. It makes a ton of choices in the service of services as and Elixir inherited them. We've traded off a number of things we don't care about to get a fantastic foundation for a web framework. We get concurrency but might have lost a bit in start time. We have consistent latencies but don't get the most optimal shared state.

I started out considering Phoenix as an opinionated framework in the vein of Django. I don't know what gave me that idea aside from them both being web frameworks. Sure, it brings in some opinions such as "specifying routers in a central place is nice" and "this is how you should bring in your helper functions for doing controllers". It also abstracts away connection pools and supervision trees and there are opinions enshrined there but that's usually not what people mean when talking about opinionated framework designs.

There are some more opinionated parts outside the core of Phoenix. Channels is an opinion on top of web sockets, Presence as well. Phoenix LiveView is heavily opinionated and makes fairly aggressive trade-offs for great wins in productivity.

Phoenix also brings in Ecto by default and Ecto is a fairly opinionated approach to relational databases. Changesets and the DSL are both quite flexible but they heavily funnel you towards a particular way of working that the library believes is best.

The most contentious part of Phoenix seems to be Contexts. Which is the connective tissue between a Controller, Channel or LiveView and the Ecto-driven data layer. There is an approach generated by default but it is much discussed and will generally require you to apply your own designs and ideas. It it very open ended. There is no special Context code. There is an opinion coming from the Phoenix generators but it seems very softly held and it is not really in the bones of the framework.

Overall I think Phoenix has been layered well and I find this restraint of opinion in the base framework means that there isn't a big desire for a lightweight microframework alternative to Phoenix (see Flask, FastAPI in Python) because if you just ask it to not add a database or skip the HTML bits or whatever configuration you need you will get your minimal API service or you full-trim HTML-spitting machine.

## The Phoenix feature set

I will try to capture the things that make up Phoenix. Major features as it were.
### Project generation

Phoenix does a bit of inversion of ownership and as I've mentioned, produces a bunch of files that you can then own and evolve. I run `mix phx.new` on something like a weekly basis as I try a new hack of some so The generator has a bunch of options 