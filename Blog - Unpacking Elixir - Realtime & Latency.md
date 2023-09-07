Elixir was built on Erlang. Erlang was built to provide "consistently low latency" and a few other audacious goals. Note, this is not a hard realtime constraint. It is soft, squishy and yet, important and real. It makes Erlang unusually suitable to systems where latency matters and where a near-realtime experience is necessary.

Soft realtime. This part of the Unpacking Elixir series will really benefit from having read [Unpacking Elixir - Concurrency](/unpacking-elixir-concurrency.html) as it covers a lot about how concurrency works in Erlang and with that it covers Processes and Schedulers. This is mostly about Erlang and the historic choices made in developing the BEAM virtual machine. In the end we will get into how Elixir leverages it in the current landscape. Shouldn't be too long.

Erlang's claim for consistently low latency predates the multi-core concurrency approach a fair bit. From my understanding it ran a single scheduler in the olden times and the pre-empting was there to ensure this fair distribution of computational resources. It would prevent single pieces of CPU-intensive work from holding up concurrent, faster, pieces of work. It ensures progress is made on all work in short order. No piece of work should be waiting long for processing. A heavy piece of work will also eventually resolve.

Another interesting advantage of this design is that an accidentally infinite piece of work will not necessarily disrupt the system significantly. It will waste resources but will not block progress.

Erlang was built for telecom and the work was, again from my understanding, about routing phone-calls without introducing delay preventing any particular misbehaving process from impacting other processes too heavily. In a way it was always about the quality of user experience. Performance often is.

It is a very high-level and dynamic system which is unusual when talking about performance. Why? By being a higher abstraction level, being very dynamic and working with immutable data structures as a Functional Programming style language Erlang leaves a lot of performance on the table in the service of other objectives. Consistently low latency is one of the objectives. And it is a performance objective. This is an interesting quirk of design. Designing for the absolutely lowest latency would require a very different approach. It would also complicate or even sacrifice other objectives. It would also be more expensive. It would likely be a much more complicated system. It would be very inflexible and hard to change.

Rather than the absolute lowest latency they went for consistently low latency, with tolerance for deviations and acceptance for imperfect results. They created a system that has been spoken of with some reverence in Computer Science ever since. It is not because it has the lowest latency. I am quite certain it does not. That was one objective. We will cover more of them.

So we have a cool high-level language with nice abstractions that offer low latency. What did people do with it? We mostly hear about chat and messaging. RabbitMQ is built on Erlang, popular open source message queue for infrastructure. The Jabber/XMPP ejabberd software was a popular FOSS chat server. Seemingly that ended up in use at Facebook for their chat. Actually, you will find Erlang or Elixir driving chat in a ton of computer and video games. League of Legends by Riot Games comes to mind.

Even more famously Whatsapp built on Erlang. And Discord built on Elixir. These are two massive players in instant messaging that are deeply invested in the BEAM virtual machine. They are applications where latency matters. Not as life-and-death, not to hit the deadline for correctly driving an electron beam or anything quite so sensitive. Rather for providing a good experience to users. It doesn't matter if there is 20ms latency more or less. It matters that there is almost never 1000ms more.

Onwards to what we can use it for. Elixir got the Phoenix web framework. Initially it was just a very responsive web framework. Especially as it pre-compiled templates in a nice way so they render really fast. Even under pressure, latencies would be good. Good but nothing shocking. 

One of the early bigger things from that was Phoenix Channels which is a bit of abstraction on top of Web Sockets. This allowed a Single-Page Application or similar to talk to a server that could keep state, that could wrangle messaging, database communication and also was good at broadcasting to other connected clients in a really simple way. Importantly, it didn't suck. A lot of them did back then. The latency was great, it scaled well. See [The Road to 2 Million Websocket Connections in Phoenix](https://www.phoenixframework.org/blog/the-road-to-2-million-websocket-connections) for more from that era.

The next big thing has been LiveView. This is again where that latency being a user experience thing comes to play. Phoenix LiveView allows you to write Elixir instead of JavaScript frontend code to a very large extent. Components, interactivity and all you really want from a frontend framework. But you write Elixir and your state only lives on one side of the connection. The server. It does require that connection and to be a great experience it requires that latency stays low. LiveView lets you do a lot with a little. It has been copied by most major web framework ecosystems. But they can't copy the BEAM runtime and as such they can't quite get the same deal.

LiveView is not a panacea. Not a silver bullet. Not the solution for every problem.

LiveView is very much Erlang. It solves a solid set of problems people actually have with abstractions that make those problems trivial to work with. It seems unconcerned with a theoretical ideal and gets on with doing the work.

And it is quite quick about it.

---

Have any latency horror stories to share? Notes, questions or concerns about this thing I wrote? Feel free to reach out through {{< lars_twitter >}} or on email {{< lars_email >}}. Have a good one.