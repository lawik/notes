My newsletter-provider Campaign Monitor, not a recommendation, they are .. fine I guess, are having an outage. And I've been on an unbroken publishing streak for the newsletter for a significant number of years. Probably 4, I could check but can't log into Campaign Monitor.

---
## Building up the Nerves

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

There are also tons of possiblities for improvement. The zero to 