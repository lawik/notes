I've kept very busy recently and as I look at what I published last it has clearly kept me from blogging. I don't love that. I like having a blog and I like tending to it. I've not missed a beat on [the newsletter](https://underjord.io/newsletter.html)'s weekly cadence but that might not be your jam. Let's catch up. I've been diving into embedded Linux with Elixir.

My [latest non-newsletter issue](https://underjord.io/the-future-stack.html) was about [The Future Stack](https://www.youtube.com/watch?v=buy_ke9s-sM&pp=ygUNZ2lnIGNpdHkgbGFycw%3D%3D). I think that idea is interesting and holds some water though I'm still not convinced anyone will solve the database without immense trade-offs. I'm pretty curious about geo-located tenancy via SQLite as a way to tune perf to customers but I don't need it for what I do so not trying it. That talk was supported by Tigris and while I still [work a](https://www.tigrisdata.com/blog/metadata-quering-with-elixir/) [little bit](https://www.tigrisdata.com/blog/eager-and-lazy-caching/) with them I am mostly wrapping that up as I have other commitments that drain my time. I really like their product and the team has been super friendly. Strong recommend. Hopefully you will see more about them from the perspectives of people I know that write well and have cool ideas.

My latest blog post was a [newsletter cross-post](https://underjord.io/newsletter-building-up-the-nerves.html). About Nerves adoption. Nerves being the embedded Linux framework for Elixir. These things happened in sequence:

Random company I'd never heard of reached out with a need for a Nerves consultant to show them the ropes and guide them in Elixir. Nerves Client #1.

- Work [Secure Boot for CM4](https://elixirforum.com/t/secure-boot-with-raspberry-pi-cm4-and-nerves/63160) devices.
- [Wired 802.1x with EAP-TLS via NervesKey](https://elixirforum.com/t/vintagenet-with-wired-802-1x-under-nerves/63228) (ATECC608)
- And me [doing a talk](https://www.youtube.com/watch?v=Yd-qWQ65eQ8) on securing Nerves devices and all the detours it involved.

Frank's team at SmartRent needed som reinforcements. Nerves Client #2.

- I've shipped [an Ambient Light sensor driver](https://hex.pm/packages/vcnl4040) in Elixir (VCNL4040).
- I've tuned more ambient light vs. backlight than I could ever dream.
- Worked with ZWave PTI (package tracing interface) but thankfully they had a Ben that solved most of it.
- I've fought Real-Time Clocks.

At this point I made a decision to pivot Underjord towards Nerves. I like a niche and heck if that ain't one. Not kicking out any clients. Just focusing in on that for future work. And specifically Nerves with a security tinge because I always find that interesting.

Then Josh Kalderimis shows up out of nowhere doing [NervesCloud](https://nervescloud.com) and somehow derails me into co-founding a hosted version of [NervesHub](https://github.com/nerves-hub/nerves_hub_web) with him. So I'm doing a startup. This was unexpected. This further links my destiny and success with the Nerves community and ecosystem.

If you don't follow my other channels you may have missed my series of [talking at you about Nerves](https://www.youtube.com/playlist?list=PLk_-dV_ai0LERQeaYYs7A5siKH4eEr4dI). Currently releasing daily. I've also been active on the socials about various Nerves efforts. I'm gearing up for a talking in Berlin at Code BEAM in October where I will be covering a lot of ground. Then I have another talk in Malmö for Oredev where I will spread that Nerves flavor to the unknowing, unsuspecting general developer public.

If you follow Elixir you might see a lot of hobby-grade Nerves being done. That's cool and fun. It is not what it was built for though. It was built, much like Erlang to be robust, production-grade, pragmatic, nuanced and practical. With that Elixir flair. And starting NervesCloud has led to seeing a new Nerves company nearly every week. Small and larger. Or companies doing Elixir on hardware that really would benefit from Nerves. Lots of interesting stuff happening out there.

And just a few days back I talked to Frank Hunleth and it was voted that I'd join the Nerves core team. It is not a shock as I've been running the newsletter for several years now and my work in the team will really be about communication more than anything.

I think Nerves is a fantastic way of building embedded Linux devices. It uses the simplest standard tool to get you a minimal Linux, namely Buildroot. Then it boots you into the BEAM and it takes the wheel. From there you generally write Elixir (or if you prefer, Erlang, Gleam). The supervision trees, the stateful GenServers, the binary pattern matching, the message passing. It all makes such sense in an embedded device where what you are developing is not just a one-purpose service. It is a full system.

Erlang was built for telecom switches that would now be very comparable to "edge" devices. They exist in inconvenient geographical places. They need to be reliable and they have a lot of stuff going on in them.

It seems like most embedded Linux projects grab an OS for their fundamental approach (Yocto, Buildroot, Raspberry Pi OS or a Debian derivative). Then the system orchestration is probably based on systemd. Then on top of that comes the wild west. Maybe Python? Maybe Node.js? Maybe C++ or Java.

Microcontrollers are a different conversation and that conversation is largely about Zephyr now it seems.

We're talking about embedded *Linux* and equivalent. Nerves brings opinion to this space. Elixir, OTP and the BEAM give you standard ways to do things. Firmware updates? NervesHub. Bad firmware recovery? fwup with A/B partitions. Web framework? Phoenix, absolutely top of the line. Web UI? LiveView and you typically lose the latency disadvantages. Ephemeral state? GenServers. PubSub mechanism? Message passing. Message passing between jobs. Message passing.

We're back at the graph from Sasa Juric's book Elixir in Action about what jobs in a modern web app can be replaced with just Erlang.

And the value of pre-emptive multi-tasking for all this is hard to overstate. The value of consistently low latency. The value of fault-tolerance.

For iteration speed during development you get a whole scale of options from host-side development through pasting code over SSH, on into deploying firmware in a minute and on into deploying firmware to specific devices over NervesHub.

I've never been more professionally satisfied. I'm up to my ears in dev-boards, weird hardware and [awesome impulse purchases](https://elixirforum.com/t/bringing-up-cool-hardware-with-nerves/64566). And consequently I'm highly motivated in helping this reach people so that there is more market so that I can run business in this space for the foreseeable future. We've got a good crew of collaborators and lots of plans.

Near team we have [a Nerves Workshop](https://docs.google.com/forms/d/e/1FAIpQLSdeUfKIhU0lXqReFEB2DC6sw0gWvSgiaw0XaZrxW-wde9n64w/viewform) in Berlin on the 13th of October before Code BEAM Berlin. No cost. And then I have my talk where [the community provided me a fleet](https://github.com/lawik/fleet) of [over 100 devices](https://youtube.com/shorts/vdrPufkXn3I). I've [joked about](https://www.youtube.com/watch?v=K23DDFreXr4) my entry into embedded. It is for real though. I want to learn all this stuff, go deeper and closer the circuit board all the time.

And it is super cool to get to improve on tools the community and commercial actors are using to manage hundreds of thousands of devices. We'll add device health information, geo-location, better deployments and so much more.

It feels weird to say but I essentially run to the office every day to get hack more on all this.

Have any thoughts or questions, you can reach me at {{< lars_email >}} or more casually on the fediverse as {{< lars_twitter >}}.