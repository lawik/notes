
Fly.io is a very visible cloud provider in the Elixir ecosystem and they put forward an interesting promise. They don't deliver on that promise currently but I think it would be very compelling if they get there. Especially for Elixir. Let's dig in.

*This work is sponsored and supported by Tigris. Object Storage that works like a CDN on Fly. They are also part of the subject of the post. You'll see. It is not mainly about them though they play an interesting part.*

This post is adapted from a talk I gave [at GigCity Elixir in Chattanooga](https://underjord.io/chattanooga-gigcityelixir-nervesconf-2024.html). In the talk I went to the trouble of trying to underline the value and difficulty of making things simple. I will abbreviate those efforts significantly in this blog post. If you don't believe simplicity has value. Come back and read this later, when you do.

I will offer Gall's Law.
(TODO: format quote)

```
A complex system that works is invariably found to have evolved from a simple system that worked. The inverse proposition also appears to be true: A complex system designed from scratch never works and cannot be made to work. You have to start over, beginning with a working simple system.
```

This doesn't just tell us that simple systems are the way to go when creating new things. It should also help you see the high value of any existing complex systems that work.

## Opinions and trade-offs

The software way to make a thing simple is abstraction. A higher-level of abstraction should be simpler than the levels below it. Using it should produce more or better outcomes for the same effort. This means sacrificing something in translating the complex to something simpler.

Either we place constraints on the allowed solutions and restrict future flexibility, remove functionality or we make trade-offs in performance, in correctness or in other aspects. I would call all of these "opinionated" approaches.

Phoenix LiveView is a highly opinionated approach to web application development with significant and clear trade-offs. It collapses several concerns into one solution: the backend logic, the API contract, the frontend API client and the DOM rendering. If LiveView fits your needs it removes a lot of concerns.

Databases are a bit of a special case here. They are the worst. And the most important. They are where trade-offs matter the most. This is also why they are the most common bottleneck. There are things we refuse to trade off.

Apparently we want to keep our data. We want it to be the same when we read it back as when we wrote it. We want it to be easy to query and manipulate. We want it to be fast. Fast can mean retrieval time for a single record, a whole-ass query with joins and also when many queries are made at the same time. We place high demand and are willing sacrifice very little with databases.

## History-time

I didn't come up in Rails but to my understanding Rails is a web framework that was willing to trade off clarity and rigour for expressiveness and productivity. It was built in a language that was willing to trade away performance for simplicity and a certain type of elegance.

Heroku came up and offered something that fit with Rails. The trade-offs made for Heroku didn't constrain Rails at all and offered immense conveniences. They were complementary and harmonious. You could shove a Rails-shaped piece in the Heroku-shaped slot without friction.

Rails became a darling of the startup space. The startup is a very opinionated solution as well. Quickly explore a domain with disposable solutions. Create MVPs, ship prototypes, find product-market-fit then achieve growth. Performance, rigour and maintainability are not even on the list of startup priorities usually. There was no friction between the startup way and the Rails way.

The trade-offs are well aligned. The Rails startup on Heroku achieves their solution in the simplest possible way. Without friction or contradiction.

About 40 years ago now in Sweden there was a wild software lab. They had the task of creating a platform to operate telecom systems robustly. The requirements included performance aspects such as low latency, concurrent operation, operational aspects around deploying without downtime and strong focus on reliability. It must not go down. This was a very different industry. Decidedly not a startup and it had very different trade-offs to consider. And from this we got Erlang and OTP.

For completeness we must quote Dijkstra.

(TODO: format quote)

```
"Simplicity is a prerequisite for reliability". - Edsger W. Dijkstra
```

Erlang sacrifices some types of performance to get immutability. Immutability is a simpler view of the world. And it uses immutability to enable a set of simple building blocks. The Process. Send. Receive. This enabled safe concurrency. From this they built up more and more abstractions in service of the desired solution. And the result lives across Erlang, OTP and the BEAM virtual machine. It is a very complex system that is proven to work. And it is clearly derived from simpler parts. It has made very different choices from Ruby and Rails. And it certainly made different trade-offs from what it was competing against: C++
## Changes

I wish the story was that the world realized Heroku, Rails and startups were too inefficient and came together to push for more efficient use of our resources.

Instead someone put JavaScript on a server and it ate Rails' lunch.

Salesforce bought Heroku.

Startups have remained a thing. Off and on. And off. And on.

Startups did focus more on growth though. And I believe the interest in massive scalability, large engineering teams and unicorn valuations did shift technical priorities. This is not where Rails or Heroku shine. And it is where cloud native got rolling.

The trade-offs made for Ruby and Rails had direct impact on performance and scalability. And Heroku was never the cheapest option. At larger scale and explosive growth your priorities change significantly. The performance you had traded off becomes a priority. The efficient scalability your MVP didn't need is suddenly strangling your progress. High cost at great scale is very concerning to a business. Even with massive VC funding.

## Constraints and friction

When we talk about a good abstraction it is always contextual to what we are doing. The Rails on Heroku startup was very happy, the hit company trying to scale on top of Heroku and Rails might be quite frustrated.

If only someone would build an ecosystem that is really productive but scales differently from Rails...

...

A good abstraction necessarily imposes constraints on you. The best constraints sacrifice things you don't want to be doing anyway. A lot of Elixir devs find that immutability makes their code easier to understand and follow. It reduces confusion. But it IS a constraint, when we apply that constraint we have lost a potential way to build things.

We certainly want nice things. In fact i think we want all the nice things.

We want elastic scalability but we don't want to operate Kubernetes.
We want a horizontally scalable database but nobody actually likes eventual consistency.
We want the speed of a cache but we don't want to deal with getting it right.
We want fantastic UI and UX capabilities but we don't WANT a single-page app framework.

If we opt in to any of that complexity it ought to be because we consider the upside important enough.

A mismatched stack of trade-offs removes all of your space to manoeuvre. If you bring in Elixir and write it like enterprise Java you are taking the worst of both worlds. Meanwhile aligned trade-offs feel like you are getting away with something. This is how a lot of people feel about LiveView, a lot of upside for no discernable downside, to them.

## What do you need?

We can only have everything if we have infinite time. And we are all very human. What do you want to spend your time on? What feels important to control? What will you pay to ignore? What power will you give up for convenience? What capability do you give up for productivity?

A project that will only run at massive scale might benefit from Bazel, Kubernetes and all that aspirational scale from day one. Assuming you can get to day two and three. Consult John Gall about that. If your project will never run at immense scale, or only ever in one country, you can trade off geo-distribution, you can trade-off future scalability and you can get a simpler system for that sacrifice.

Erlang was built for industry. It traded off particular performance and strictness aspects for an incredibly powerful runtime that excels in building robust services. It is a practical tool that doesn't even try to be idealistically perfect.

Elixir is also an industry language. It doesn't pick a fight with the trade-offs that Erlang made. It invests in them to enable more in the realm of services. Phoenix doubles down on that for web development. LiveView triples down on that for web app UI.

At any point one of us can feel that one of these constraints chafe. That's the point at which our specific need might diverge from the most opinionated approach. This means it is on us to either peel back a layer and accept more complexity or find another compatible approach with a better match in trade-offs. We can usually shift a trade-off or two. The canonical case is probably Discord bringing in Rust to optimize a particularly demanding data structure in their Elixir system.

## Elixir and Opinions

If you take the constraints brought by LiveView you will find that it is a sweet-spot for a solo or small team building a web-based SaaS. An Elixir-developer can thrive end-to-end in such a project and reap many of the intended benefits of the abstraction. Bumblebee from Nx sneaks in and starts to expand this picture for many machine learning needs as well.

Phoenix is not extremely heavy in opinions. At least not technically. We debate Contexts as a design decision and they are fundamentally quite open-ended. They don't impose constraints and they don't deliver much guidance.

I have seen a fair number of developers point to this lack of opinion as a problem. To them this is a wide open space that should be filled with opinions to give direction and achieve further gains. Other developers appreciate the space to do whatever they prefer. Some find the conceptual guidance on contexts enough to work off of.

I just recently watched Keathley's talk from last year and I appreciate his willingness to just collapse the layers at the controller. Some people will love it and some will hate it. A clear signifier of an opinionated approach and active choice.

If you want more direction and are willing to accept a lot of new opinion from elsewhere you can add Ash Framework to the stack. It provides constraint, guidance and plenty of opinion. It will fill out everything between Bandit and Ecto if you want it to and it brings ideas on how authentication, authorization and tenancy works. Ash is interesting in this regard because you will find people that really dislike the approach and people that are all in. This indicates a framework that puts forth a strong opinion.

You want to make trade-offs that cost you as little as possible. Technically, which means humanly.

And depending on your team you may find that some technical opinions and constraints land very differently. It seems that generally developers with less experience benefit from more clarity, guidance and opinion. They are still learning and making technical decisions is quite difficult and demanding from that vantage point. More experienced developers might find constraints they disagree with quite cumbersome and frustrating. They may have other strong opinions they'd prefer to apply. They might have pointier and sharper trade-offs they want to make.

Whether you should let your experienced devs go wild in this regard is a .. team question.

## The Platform

I think this idea of low-cost trade-offs is also what makes Fly interesting to the Elixir community. If we already take on the trade-offs of LiveView and the general strengths of our platform, the BEAM, we know that latency matters. And latency has always mattered. Everywhere. The more we can cut down on latency the better our chosen trade-off with LiveView works. Fly's whole vision is to put a web app close to the user.

But of course that is not enough. Because your app is generally not only your application server. You at the very least have data and files.

Today most developers want a managed database and S3-compatible object storage. These have become table-stakes.

S3 made object storage a thing and is a prime example of a simple solution. And it blew away vast complexity that came before it. Typically NFS. Which was terrible. S3 gives a starkly limited API that enables the cloud provider behind it to scale essentially infinitely and offer great reliability. But one thing S3 does not offer is great latency, especially if you only store your files in us-east-1. Did I mention I live in Sweden?

So do you take on the complexity of adding a CDN for your files? For my sake?

The people I work with at Tigris created the object storage solution offered on Fly now. They are essentially collapsing the concerns of object storage and a CDN into one. They make the assumption that you want both. They offer geo-distribution by default, both on upload and download. That is their contribution to the vision. Aligning with the Fly proposition.

They offer a very simple service which does a lot for you. The complexity is hidden behind 18 years of progress since S3 launched as well as aligned trade-offs.

I tried to push them on what they are sacrificing to provide something more than S3. And they didn't feel there was a sacrifice. Which seemed suspicious. So I dug in. And I think I know why now. They are selling you something. Literally. The fundamental selling point of cloud-based object storage is that you do not have to worry about disk space and you don't have to worry about losing data. Infinite storage, great reliability. Offering this requires taking on severe operational complexity.

I do not want their job.

The work Tigris has done for low latency centers around FoundationDB which is very capable as a scalable metadata store. Most of the challenges with FoundationDB are operational. Tigris are doubling down on managing operational complexity. Aligned trade-offs can feel like no trade-offs, to them it is all the same. Keep stuff running well.

It seems object storage is handled.

The managed SQL database part on Fly is unsolved. And Fly has clearly tried to solve this a few ways indicating that, yes, this is important to them. Fly has a managed database via Supabase now and that's cool, good. Their old one is kinda gnarly so Supabase should be much better. But Supabase is not a magical geo-replicated database. Supabase is Postgres. It doesn't complete the vision.

Fly are trying some stuff with SQLite and LiteFS which is interesting. But I have worked a bit with the Electric SQL folks as well and I think their approach has a lot of potential for this geo-distributed concept. CRDTs are very powerful if you accept eventual consistency. And Electric SQL launched pglite, which has the eye of both Supabase and Neon. So we might see something interesting happen there. Postgres on every server perhaps? I did that as an experiment with their SQLite variant.

Some people have brought up CouchDB (the DB that did everything first, in Erlang) which is not SQL. Cassandra, which is closer to SQL from what I gather but not quite, Discord switched to ScyllaDB from that. And related to all this Phoenix will soon ship with [CockroachDB support](https://x.com/lawik/status/1791389748243648628). All of the options have fairly significant trade-offs but might be great for your workload. I think elasticity and reshaping the cluster is kind of high on the list to fit the Fly model or you need to run a node in every region you want low latency, continuously. This is not typically how these work.

So why does it matter if Fly and their partners pull this off? I think it makes for a very rare alignment of priorities and trade-offs from end to end. Platform, runtime, frameworks and language all sharing values and expressing a similar overall shape. Meaning Elixir would be unconstrained by the platform and the platform doesn't offer a bunch of complexity that is irrelevant to a typical Elixir app. This should give us trade-offs that cost as little as possible. You should be able to put a Phoenix-shaped app in the Fly-shaped hole without friction.

And hilariously good latency for users.

It matters because the goal-posts keep moving and the minimum expectations keep going up. What has worked up to here will struggle in the near future. The need to make consistent trade-offs and leverage them as much as possible is especially important to be able to work as small, highly performant teams.

It would be a true new Heroku but for Phoenix. I will leave the part about finding an aligned business model to replace the startup as a question for you all because I don't know what that looks like but I am very curious. I think indie hacking and open coops could both fit. But give me your suggestions.

I am not trying to sell Fly. They do that well enough. I am selling Tigris a little bit but I don't really need to. I have high hopes that Tigris will prove themselves out regardless.

I think the overall idea has interesting implications for the mainstream web SaaS side of the Elixir ecosystem. Other applications will need very different infrastructure. I really like working with Nerves which is quite different. I know Elixir performs hilariously well on bare metal and that's a different approach. It all depends one what you are building.

## Build or Rent

Personally I am always tempted to DIY anything and everything. I don't want to rent infrastructure. I want to build it. This is my desire for control, knowledge and the satisfaction of building something. In practice I typically go with something more sensible, more pragmatic. At work one of my constraints is that I do not have eternity to build everything. Instead I have deadlines. So I pick something where someone made choices and placed constraints. Even if I don't want to.

And the trade-offs of cloud services, AWS, Fly, Tigris, all of them, is to take operational complexity out of your hands for money. They offer a constrained API surface you can interact with to fulfil your needs. They will leverage the constraints their system places on you to achieve efficiencies in scale and manage that operational complexity for all their customers, all at once. Hopefully as a complex system that works.

Most of us are willing to pay some amount to remove operational complexity but how much and in what way varies a lot. There is a scale from the rented dedicated server to a Heroku-style platform. Sometimes it even involves Kubernetes.

It goes back to managing complexity. The demands on our software systems only increase. As we build more software in response to those demands we need to find ways to keep things as simple as possible. To raise the abstraction level of the work. Ideally we want to keep reaping rewards from trade-offs we've already made.