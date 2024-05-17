In this part of the series we dive into one of the frameworks and toolsets that really got me going with both Elixir as a hobby and the community as a space full of helpful people. This will be all about [The Nerves Project](https://nerves-project.org/), oh, and touch on some related stuff.

Before we dig in I should shout out some other BEAM, Elixir & Erlang-related things in the embedded space. [AtomVM](https://www.atomvm.net/) seems super cool and lets you do Erlang and Elixir on microcontrollers. ESP32s and such. I haven't tried it. I really should. There is also [the GRiSP boards](https://www.grisp.org/specs/) which does interesting stuff to push Erlang's soft real-time performance closer and closer to hard real-time. It also works with Nerves if you prefer some Linux in the picture.

Erlang has been used for embedded Linux for a long time. The language started in the telco world which blurs with networking which blurs with industrial hardware. It all blurs with "embedded linux" which blurs with IoT and connected devices overall. It should not be surprising to hear that there are a decent number of people who have done custom linux + Erlang systems in the realm of embedded linux. I don't know of any that have established themselves as the tool of choice or built much of a following though.

I guess Frank Hunleth is one of those people. He did a bunch of embedded, mostly C and C++ before he started veering off into Erlang. Since Frank is a sweetheart we've [had him on](https://www.beamrad.io/9) the pod [twice](https://www.beamrad.io/64). I think the first one should give most detail on his professional history. When Elixir showed up there was more interest and more engagement there and Frank pivoted his IoT tooling plans towards where the collaboration and community felt most active. So Nerves was born into Elixir.

What is Nerves then?

It is Linux as mostly a hardware abstraction layer, source of drivers and OS fundamentals. Then it starts the BEAM and you take care of the rest from Elixir or Erlang. The linux variant is a custom Buildroot system. Buildroot is the less complicated of the major embedded Linux toolsets. Nerves offers a bunch of prebuilt systems adapter to particular hardware. So the Raspberry Pi 4 will have different drivers and configuration than the Beaglebone Black. It supports all the Raspberry Pi computers (not the Pi Pico microcontroller) which means that if you have one in a drawer, time to bring it out. You can also roll your own system if you know enough or are very willing to learn hardware and Linux.

The development process is just an Elixir project. Depending on how hardware-reliant your application is this can vary a bit. Nerves makes it all easy. Trivial SSH into the device. Pushing firmware over the network so you don't need to swap SD cards for every change. A/B partition updates to ensure it is very hard to bork your device in production. It really does have great tooling and a clean process for what is an involved type of work. And of course you can just paste code over SSH into iex. The best deployment process ;)

I got into Nerves by porting an eInk display driver from Python (which I knew) to Elixir (which I was learning). The Elixir Slack channel for #nerves was super helpful and friendly. At the time of writing I have been paid for Nerves work several times and am currently enjoying ongoing Nerves projects with clients. I really recommend trying some Nerves if you either enjoy tinkering with hardware OR have been looking for a reason to try Elixir.

Okay. So it boots the BEAM. And that's your systemd-equivalent or init system. Most of your system should be Elixir code with all the expressiveness, effortless performance, consistently low latencies and reliability that entails. Compared to anything I've set up on a Raspberry Pi before it is also a wonder of determinism. Your deps get locked. It becomes a real software project, not just a bunch of random tutorials you run through.

There is also NervesHub which is an open source, self-hostable tool for managing fleets of Nerves devices. Firmware delivery, binary diffing for minimal updates, reverse consoles for troubleshooting. Authenticating devices based on hardware device keys, aka. a secure element, hardware root of trust (see [NervesKey](https://hexdocs.pm/nerves_key/readme.html)).

Nerves helped me get into the Elixir ecosystem and community and it keeps giving me wonderful new opportunities to learn and explore. It really holds a special place for me. Part of that has been the immense generosity of Frank, Connor and the rest of the Nerves core team folks with both their time and knowledge. I've learned so much and scratched many itches thanks to them. I hope to pay it forward both in software, time and effort.

There are already many examples of Nerves:

- SmartRent
- [SparkMeter](https://elixir-lang.org/blog/2023/03/09/embedded-and-cloud-elixir-at-sparkmeter/)
- Rose Point (navigation)
- [Bowery](https://www.hashicorp.com/resources/connecting-farms-and-growing-plants-at-bowery-farming-with-nomad-and-consul)
- [GridPoint](https://elixirmerge.com/p/gridpoints-use-of-elixir-and-nerves-for-energy-management)

Most of the companies using Nerves do so quietly. This seems to be a thing, especially in industrial automation.

The Nerves ecosystem covers a pretty reasonable amount of hardware with good Elixir-based drivers or libraries. In cases where they are missing the Circuits libraries provide all the necessary primitives with I2C, GPIO, SPI, UART. Elixir offers some really exceptional binary pattern matching and binary construction patterns that make working with hardware incredibly smooth.

I think Nerves stacks an incredibly reasonable set of practical solutions on top of each other and gives shape to what might other be hard to approach. If you are curious to learn about embedded linux I think just the abstractions and structure itself is helpful and if you experiment with it you will come away wiser.

Easiest ways to get started is with Nerves Livebook: https://github.com/nerves-livebook/nerves_livebook

I made a demo video for it a while back.

TODO: VIDEO

So go touch some circuitboards. Enjoy yourself.