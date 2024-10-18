My newsletter-provider Campaign Monitor (not a recommendation, they are .. fine I guess) are having an outage. And I've been on an unbroken publishing streak for the newsletter for a significant number of years. Probably 4, I could check but can't log into Campaign Monitor.

---
## Building up the Nerve(s)

As we are doing [NervesCloud](https://nervescloud.com) we are thinking a lot about adoption for Nerves. Our market is tightly bound to the number of folks doing IoT/embedded projects and choosing Nerves. The business can only thrive if Nerves thrives. Nerves can only thrive if Elixir thrives.

I think there are enough people working effectively at Elixir adoption and it is a long path as a community with no massive commercial backers. It is doing well, it is healthy. Currently Nerves i mostly a smaller circle within Elixir. There is a small part of the Nerves pie that is entirely from outside of Elixir. Other embedded developers coming into Elixir by way of Nerves, rather than moving from Elixir into Nerves. Both of these are valid adoption paths.

The Elixir to Nerves one is the one I feel comfortable with. I've lived in that population for 6 years and know the Elixir community & ecosystem well.

The other end is more mysterious to me and I'm getting into listening to embedded podcasts, YouTube videos, blogs and trying to get familiar with that space.

Growing Nerves will require both. Partially because we do need to grow the entire pie. But also because we need the mix of experiences.

Most people moving from Elixir into Nerves are comfortable with web dev. Usually some databases, distributed systems and generally web service-oriented stuff. Most are way out of their comfort zone moving into custom Buildroot systems but can certainly learn to add their supporting libraries and compile a new system. I would imagine very few, including myself, would be confident that they could bring up a new board from one of the industry vendors.

Most people moving from embedded Linux as a general practice into Elixir are going from C or C++ and heading into a more high-level approach. They live and breathe the low-level stuff, they don't mind dealing with Linux, device trees and so on. What Elixir offers them is a productive high-level abstraction for stuff like UI, orchestrating various processes and communication in a crash-resistant packaging. They can unleash developers that don't know C on building features for their device without the risk of them segfaulting the hardware.

It is fairly uncommon to find people that cover this end-to-end. I think a team building with Nerves will get the advantage of these people meeting in the middle, somewhere in the Nerves firmware and cross-pollinating experiences. Shared language, shared concepts and easier shared understanding. Less need for hard silo walls and formal boundaries due to technical domains.

I think these are the fundamental paths to adoption through developers. Then there is always reaching decision makers and them pushing for adoption but I'm not convinced that that's very useful without technical champions pushing for the tech. And arguably you could get people that arrive cleanly without Elixir experience and without embedded experience. I don't have high expectations there but if we hit some exceptional viral vein, sure, then that could be a factor. That's a lottery and I don't think it is smart to bank on.

I find Nerves to have very good fundamentals. A developer from embedded who've used Buildroot before will find that it doesn't actually stop you from doing anything you usually do with Buildroot. Developers from Elixir will find that most stuff is just like any Elixir project. The Nerves libraries and tools support the area linking together the embedded side and the Elixir side.

There are also tons of possiblities for improvement. The zero to prototype story is quite good. But it could be a ton better. With more capable generators, more opinionated starting points and so on. I see us getting the TTWK, Time To Web Kiosk, down to 5 minutes, counting from the moment your Seeed ReTerminal DM gets delivered.

Nerves is mildly but somewhat firmly opinionated. And the opinions it has provides a lot of capabilities for NervesHub to provide a bunch of utility. Remote IEx console is a heck of a tool. Firmware updates, firmware deltas. And we can do a lot more here. There is so much we could offer by default for a NervesHub-connected device. There is also a NervesHub CLI, that one could be improved a bunch as well.

Building the good features and functionality, improving everything for the best possible DX and productivity are all great tools for adoption. If the thing is not any good and doesn't feel any good people will bounce off of it, if they even try.

Unfortunately the world is very noisy. You can be deep into embedded and never hear about all the fantastic tools out there. I am continuously surprised by how many folks in Elixir have not heard of Nerves, Membrane and even stuff like Nx. It is a big world and being noticed is difficult to achieve.

This is where conference talks, YouTube videos, novel stunts like the 15 minute blog in Rails come in. Just to break through and catch enough eyeballs. Inside the community and ecosystem you are in, and outside.

Both quality of the thing and outreach for the thing are important. And then we have brand.

The way the project comes across. There are many ways to do this. HTMX is an example of a very unusual brand built on memes. Elixir is known for a pragmatic and productive approach to Functional Programming. SQLite is known for the database being strong and the flesh being weak. Postgres is steady and reliable. Some projects try to be cool. Others try to be sweet and friendly. The follow-through on this is a mix of how docs and resources take care of you, how the community meets and treats you and visual aspects of branding.

Some stumble into their brand over time. I don't know that Postgres has intentionally built a brand. They built their thing well over many years and they have a culture and process that produces a certain something. It is not particularly about the elephant in their case. It is not the Nike swoosh. Modern projects have to be a bit more forward about this stuff, at least if they want to grow and gain adoption.

I don't know that Nerves knows what it is brand-wise yet. Curious to figure that out because it needs to speak to both the embedded Linux dev and the Elixir dev. It needs to show the clear advantages of a lot of very nuanced choices in a deeply complex field.

Challenges ahead in other words. I'm pretty excited to try and find my words on these topics and learn to speak coherent embedded. I think I speak decent Elixir at this point. Though my first talk in this realm will be in Berlin to a Code BEAM audience, so certainly starting out at home, in my own community.

Thanks for reading.
