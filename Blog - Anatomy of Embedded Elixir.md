Working on [the Nerves project](https://nerves-project.org), the Embedded Linux framework for Elixir, has given me an increased appreciation for how Frank Hunleth and his collaborators through the years have structured things. And while I've found crossing into the Linux-heavy part of it difficult and frustrating there has been reasonable steps to take all the way from building the application layer in Elixir all the way back to fighting the bootloader. I'll try to detail how a Nerves system is built up in this post.

## The Erlang of it all

The reason to do Embedded Linux with Elixir is the BEAM. And yes, I have to type Linux every time or someone goes "hurr, hurr, oh you meant Linux, not real embedded, that's microcontrollers only", some are friendly, some are not, all are compelled to intervene. Anyway, the BEAM virtual machine treats your application as a system, and makes it possible to operate it at runtime and while this is powerful in your web app it is really important in Nerves. Your code gets assembled into **an Erlang release**. A tarball of various bits along with the necessary runtime parts.

That release contains many Erlang Applications. Some are system applications such as `os_mon`  the OS monitor, `alarm_handler`  the .. alarm .. handler. Then we have Nerves applications that come from Nerves-related libraries. Many of these will generally work on any Linux OS but were developed for use with Nerves. Some provide facilities that other applications usually perform in Linux. These generally allow you to integrate the system-level information into your application in a nicer way with message passing and API structure we know and love:

- [nerves_time](https://github.com/nerves-time/nerves_time) manages time, NTP, storing latest known time across power going down, other time sources.
- [vintage_net](https://github.com/nerves-networking/vintage_net) Good ol' networking. Manages Linux networking but from Elixir. This does delegate work to the regular network stack and tools by calling `ip` or `wpa_supplicant` as necessary. However we can react to activity in the networking layer as message subscriptions and much more.
- [nerves_runtime](https://github.com/nerves-project/nerves_runtime) is probably the most Nerves-specific as it bundles a number of important bits. Among them [nerves_uevent](https://github.com/nerves-project/nerves_uevent) which replaces `udev` as a way to load drivers as requested and \*gestures vaguely\* *other stuff*.
- [nerves_hub_link](https://github.com/nerves-hub/nerves_hub_link) entirely optional but used if you use NervesHub for firmware/OTA updates.
  
Then there is usually your app. Let's call it `my_app`. It can be one or more applications along with any applications provided by your dependencies. Generally if a dependency manages it's own lifetime and state it will run an application (NervesHubLink is for example quite independent, by design). If you should manage it's state and lifetime, it will run in your application supervision tree (Ecto Repo, Phoenix's Endpoint and Phoenix PubSub are typical examples). Because applications are independent parts of your system you can start and stop them. They can depend on each other being started.

Each application has it's own supervision tree meaning it can run multiple things and decide how they restart on failure and that they fail independently of other applications if the entire application fails. Your Nerves system can start the Erlang release and your main application can fail to start. The system can still connect to your update system, it can still correct the time, you may have a mechanism telling it to reboot from this broken state at some point but a programming error that breaks at runtime due to some stray piece of data lodged in an unexpected way will not prevent you from sending out an update.

The BEAM replaces systemd and any other orchestrating entity in a Nerves system. This means that if you run external binaries you probably integrate them into your supervision tree in some application using [MuonTrap](https://github.com/fhunleth/muontrap). You can even integrate container runtimes and run containers in a similar manner.

This is the Erlang release and the small pile of applications it ships. It includes:

- Your Elixir application(s)
- Your regular dependencies applications
- Your Nerves-related dependencies
- Built-in Erlang applications you depend on

## erlinit

PID 0 in Linux, the thing that starts everything needs to do a bit of work. In Nerves we have [erlinit](https://github.com/nerves-project/erlinit) that takes care of mounting `/dev` and other duties before starting the Erlang release. So this is a thin sliver under the Erlang release on top of the root filesystem.

## Partitions

The official Nerves systems boot through a boot partition with the necessary files, resources, device trees and whatnot required to start Linux and then that mounts the SquashFS root filesystem and runs (erl)init from there.

This partition structure is shown in the Nerves system's `fwup.conf` ([rpi4 example](https://github.com/nerves-project/nerves_system_rpi4/blob/main/fwup.conf)) and if you check you will see it has Boot A, Boot B, Root A, Root B. And this is for the A/B firmware update scheme used in firmware updates. A new update is applied to the partition not currently used (let's say B) and then we reboot with B as the primary partition. If that goes well we persist the change and all is good but we know we can switch back to the previous version if something is wrong.

## Where the Linux at? Run-time vs Build-time

Between the partitions and erlinit. We've mostly been concerned with the run-time system now. All this stuff also has a build-time component to it of course but they are run-time abstractions in my mind. They are meant to be in the system when it runs. And Linux, the kernel, weaves in as the boot partition gets mounted by whichever boot loader you have and then you tell Linux where to get your rootfs and it will go and start erlinit helpfully for you. But explaining starting Linux without mentioning the partitioning first seemed unhelpful.

Now we mostly leave run-time concerns.
## The firmware

The .fw file used by [fwup](https://github.com/fwup-home/fwup) is quite simple. Coming up with the ideas behind it was probably less simple. It is a zip archive with a file called `meta.conf` and a directory called `data` which contains files that will be used in writing the final image. The conf file contains some useful metadata fields and then a bunch of instructions for writing the partitions and putting the files from the data directory onto the card. These instructions include hashes for the file contents. Knowing the files, sizes, byte offsets and everything makes for a very fast and lean write process. That makes it feasible to use for diffs and is why fwup is really fast at writing firmware. It also makes it possible to stream firmware writes. Good primitives give a lot of nice properties.

This is all defined in `fwup.conf` in the Nerves system. If you've read the `fwup.conf` the `meta.conf` produced will make a ton of sense.

Now we entirely leave runtime concerns.

## Your project

We are now looking at build-time things, scaffolding and tooling and we need to revisit how your stuff fits in with the Nerves stuff. First time users of Nerves will just generate or clone and Elixir project with some Nerves stuff in it and write Elixir code typically. They build with a new command `mix firmware` when they want to run on hardware but the project is fairly normal. The config has a mildly different structure usually with a distinction between `host` and `target` but nothing wild.

Your project simply has a dependency on one or more hardware-specific Nerves systems depending on which hardware you support. When building, a pre-built version of the system will hopefully be pulled, your Erlang release gets plopped into the rootfs, the `fwup.conf` gets applied to generate `.fw` firmware file for you.

## The Nerves System

Maybe the Raspberry Pi 5, the TI SK AM62B-P1, the Seeed Studio ReTerminal DM or a good ol' BeagleBone Black (or Blue, or Green). They all get a specific Nerves system for them and if you build a variant of the hardware you get to make a variant of the system to match your customizations. These highly specific systems make for a knowable setup for everything that concerns you hardware that is easy to own and maintain in the long term.

There are a bunch of bits and bobs that vary per system but typically you will find:

- `nerves_defconfig` 
  Buildroot configuration for what gets included in the system, system applications, shared libraries, lots of stuff.
- `linux-x.y_defconfig` 
  Linux configuration for what gets built into the kernel, drivers, modules overlays and whatchamacallits.
- `linux/` 
  A directory with any patches to Linux that the system needs.
- `fwup.conf` 
  As previously discussed, defines the partition and composition of the firmware image.

The specific system builds on a more general [nerves_system_br](https://github.com/nerves-project/nerves_system_br) a base to hold the general buildroot config. It also contains any patches to buildroot packages of which there are typically a few. Not everything is suitable for upstreaming.

You generally don't need to look at the Nerves system unless you need to customize drivers or ship extra code that you can't get from your Elixir dependencies. Even more rare that you need to touch the base `nerves_system_br`.

## Buildroot itself

Used to pull together a Linux system and provide a way to add and configure system-level components at build-time [Buildroot](https://buildroot.org/) is a kind of old-school project but active and going steady. From my understanding it is also very straightforward in how it is put together. I know enough to use it in Nerves and I could probably use it vanilla if I tried but I'm not good at Buildroot yet. Also not good at Linux config yet. Before I did Nerves I'd rarely even whispered about recompiling the kernel. Now I do it pretty much daily for various projects and experiments.

## Tooling and framework

The concept of a System is a Nerves thing but what it does in total is not. The base system of `nerves_system_br` and the specific system for your board together encompass something every Embedded Linux project needs and in the end creates. Either implicitly or explicitly:

- A way to acquire a built Linux kernel with all relevant drivers for the hardware.
- A way to include other software.
- A way to specify the partition scheme, including boot concerns.

The system lays out what it does very explicitly, flattened. It has minimal indirection. It stays manageable by being as small as feasible. This is an intentional design decision. Elixir is your app layer and then when you need to pop the hood of the system it is all layed out there. There are not a sprawling system of interlocked smaller hoods to pop in the right order to get at whatever gasket is fraying.

To go beyond the fundamentals there is the tooling for integrating with Mix and further smart choices, such as using fwup, which enables further features, niceties and good practices. I should get into all the design choices made to keep your Nerves firmware from becoming a brick. There are a lot of choices that serve that specific purpose. That's for another post.

## Wrapping up

A statically defined, mostly deterministic, set of parts make up the system. Add your code on top. It compiles to a lean, compressed, firmware definition that can be signed and wrangled as need be. A single artifact that wraps up you entire device firmware and how to update it.

