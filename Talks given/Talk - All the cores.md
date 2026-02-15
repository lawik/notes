TODO: Clock it!

## The Wisdom of Nerves

Nerves is Elixir tooling providing best practices for embedded systems of a particular weight-class. It addresses the space where you'd typically see embedded Linux. And Nerves is built on top of Linux. I would describe the layer separating you from the underpinnings of your Nerves device as "lean". The abstraction is thin. There is not a lot of unnecessary stuff there. But it is strong and capable.

What this means is that learning Nerves starts out a lot like Elixir development. But as you go deeper and use it more you are introduced. Piece by piece to good practices for embedded devices and a very pragmatic approach to setting up an embedded Linux device.

Nerves is built on top of Buildroot which is a system of makefiles and configuration that let's you build a full embedded Linux OS with the bootloader, the kernel and the root filesystem.

Most of the times I've hit something that is a bit annoying in Nerves it is that way with good reason. For example changing the partition table to make more space on your device is a bit of a nuisance. Why? Because the entire partition table is explicitly defined, known and managed. As your system grows and evolves your Nerves system can be maintained and there will be nothing in it that needs to be a mystery to you. It is very explicit and stays minimal.

If you ever feel like modern development in the backend or web world is taking you further away from the in-depth technological bits that you love. Like I have? Nerves is pretty much the opposite. I've been moving ever deeper. This talk covers a new depth for me personally.

## Who am I?

I should say. I'm Lars Wikman. I am on the Nerves core team. I'm a founder of NervesCloud. I run Underjord, host BEAM Radio with some good friends and apparently I run conferences now.

## The collab

The work underpinning this talk could not have been done without the sponsor pairing of GleSYS and Ampere. When I chatted with local data center and cloud host GleSYS about them sponsoring Goatmire they wanted to know what might be interesting to you all, Elixir devs, specifically. When they mentioned their Ampere-based offering I was intrigued. I've heard about Ampere, the ARM servers. I knew the recent ones have hilarious numbers of cores. And Elixir devs like having lots of cores, because our VM lets us use them. So we spoke to Ampere and now they are here, co-sponsoring the event.

I was going to give a talk here, I knew that but I hadn't decided on what. So I asked if I could borrow an Ampere One 192-core machine to do something with. They graciously agreed.

My first thought was of course.. how does the BEAM run on 192 cores. Concurrency and all that. I have some notes on that but it's not a topic I went deep on.

But I saw a more interesting angle. This is an ARM64 device. What do I do with ARM64 devices? I put Nerves on em. And while it would fun to treat this as a single massively overbuilt Raspberry Pi. It would also be ridiculous and not particularly useful.

There is another upside to it being an ARM server. It should virtualize ARM64 very efficiently. So the question became: How many virtual Nerves devices could I run on this thing? And could I update them with NervesCloud, our update service for embedded devices?

So thanks to Ampere and GleSYS for getting me access to the Ampere One. Let's see what I could do with it.

## What it takes

Virtual machines. Creating software-based computers inside your computer might seem really inefficient. But they have some very practical uses and a lot of work has been put into making them an efficient, useful reality.

I'd need to build a new Nerves system targeting the virtual environment as if it was a new little physical Nerves board. So what does this entail? The BEAM-level parts of Nerves do not care a lot about what it runs on. We only need to lay the groundwork of making sure our version of Linux is built with drivers for the virtual hardware. And we need a bootloader.

First I needed to confirm what virtualization I should use to know what we were trying to target. I fairly quickly found qemu as the most reasonable starting point. It is a command-line tool that you can use to run a virtual computer. It can either boot Linux for you, skipping the need for a boot-loader or start the boot-loader of your choice. We'll revisit that. You provide a disk image, just a file, that it will use as the virtual computer's physical disk. You can specify the number of cores, the amount of memory available and more.

The physical computer is called the host. The virtual computer is called the guest.

Because qemu exists on ARM64 and has support for ARM64 as a guest we should get something reasonably efficient. At least in theory.

My first experiments with qemu was using just Buildroot which can build Linux and a root filesystem for you. It is foundational to Nerves. So I started out testing it separately. With this I could confirm that an emulated ARM Cortex A53 worked. I could get to a Linux root shell.

(EXAMPLE command-line)

I looked up Linux virtualization support and found KVM. Kernel-based Virtual Machines. It is support in Linux for running virtual machines with low overhead. It utilizes hardware virtualization support provided by the host CPU. Can you use qemu with KVM? Yes. Turning it on was as easy as adding accel=kvm and changing the CPU type to `host`.

(EXAMPLE command-line with KVM)

It starts significantly faster. It performs better and drops about 500 Mb of memory usage per guest as we don't have to form a fictional computer from whole cloth. Essentially we are running our guest OS in isolated memory regions on the host computer. We've removed a lot of work from qemu.

If you are using an x86 machine, like a typical Intel or AMD you would not be able to use KVM to provide a virtual ARM64 machine. You'd rely on emulating it. But you could do the corresponding thing for low overhead virtualization of x86.

This was all surprisingly straight-forward. Moving the correct config over to my Nerves system and removing the unnecessary stuff and then fixing all the stuff I screwed up.. that took more work.

And I was also looking at a boot loader.

## Boot loaders and Nerves

I could actually launch Nerves on qemu without a boot loader. Qemu knows about Linux and you can just pass it a kernel. It worked perfectly fine. But a lot of the power of Nerves is in how it protects you from screwing up updates and I can't really shill our service, NervesCloud, without doing updates. And what determines if the device boots partition A or B? The bootloader.

Running without a bootloader would not simulate a full Nerves device. And I needed that.

Typical production Nerves systems use u-boot. A tried-and-true bootloader popular for embedded devices. Raspberry Pi are very different, let's not go there.

So I started fighting u-boot and I struggled. At this point I understand it okay but I don't wield it confidently.

I asked Frank Hunleth for help. I think it is a sign of maturity that I actually reached out to him before I ever got the server and said "I will definitely struggle with making this work in time. Can I depend on you to unblock me and bail me out?" He agreed. And he delivered. Both in unblocking me and in the strangest help I could have hoped for.

Frank and I are wired differently in several very real and important ways but we also find common interest. We definitely both like some novelty and challenge. Making u-boot work would not have been interesting or difficult for him. He has done that a lot. So while I was trying to make u-boot work...

He. Built. A. Bootloader.

It is called little loader. It is quite simple he tells me. I helped test it. I have read a bunch of the code. I have discovered interesting compilation bugs. Suffice to say. We had a very peculiar type of fun working through this.

Is it strictly better than u-boot? Unlikely. Is it more understandable? Almost certainly.

In the end it worked. A bespoke boot loader built on Nerves conventions but potentially easy to adapt to any qemu usage.

(EXAMPLE of qemu call with little loader)

## Bundle of Nerves

We had the boot loader working only with cortex-a53 emulation at first due to some misunderstandings around ARM Exception Levels.

At this point I ran some tests. We ran 500 Nerves devices. I made a blog post.

(TODO: let the demo run either KVM+host or cortex-a53, show memory usage)

500 wasn't me hitting a limit. It was a fairly arbitrary test of running more than 1 per core. It worked. I was happy. And the next day I hoped we had KVM working.

We did. This dropped memory usage from more than 660 megabyte per VM to very close to the assigned memory allocation. I think at this point a single machine needed about 170 Mb memory to boot which wasn't ideal. We only have a single terabyte of memory!

We could easily run a couple of thousand devices though. But there were still some pretty low-hanging fruit to shrink memory usage.

I tried a few things. The erlang scheduler options here should reduce fragmentation and consequently total memory usage:

(TODO: example of options)

I tweaked Linux memory configs to be less wasteful:

(TODO: systemctl.conf)

We added Linux `zram` memory compression to the kernel running in Nerves.

(TODO: zram changes)

## Memory usage, the limit

At this point I could have the VMs start with 110Mb of RAM. I've since bumped it to 150 again because it would occasionally cause problems. There is some amount of overhead on qemu and whatever else ends up happening so I saw this get us to around 5000 devices without issue.

Then I started looking at whether we could do something about all the redundant memory we had on the host.

See we are starting 5000 identical Linux kernels with 5000 identical operating systems that will load 5000 Erlang VMs to load 5000 copies of identical BEAM files. There will be slight differences in many places but there will be a lot of identical data in memory. Maybe the host can do something with this?

The answer I've tried. KSM. Kernel Samepage Merging. It takes the host half an eternity to find all the pages to deduplicate. But it does reclaim a lot of memory as it keeps going through. Of course you are brutally oversubscribing your actual resources so if things start to divert a lot you'll find out real quick as the Out Of Memory Killer starts rampaging across your VMs bringing them down brutally.

With this I hit 7000 devices. And I had them all connecting to NervesCloud. Then our alarms went off, so I'll stick to showing 5000 for now. I think we could get to 8000 or 9000 on the host machine if they do literally nothing. NervesCloud could certainly handle a lot more.
## CPU

A note on CPU. We know of production Nerves devices running at around 200 megahertz. With 5000 devices the devices have essentially a 125MHz slice of the CPU. But presumably it could boost to 3.2GHz if qemu, KVM and Linux are smart enough. The challenges are mostly seen when all the devices are doing work.

I tend to start them in batches to avoid ridiculously high load numbers. But I've never seen the server even get particularly unresponsive. I don't think 5000 devices would be very happy if they were doing heavy work. It would be slow and I imagine CPU caches would get very sad and all that. Brutally oversubscribing should push performance off of a cliff. For a more reasonable, lower, number of VMs it should be very nice and snappy.

## Let's try it!

So this is a frontend I published for pulling the various levers. When I was experimenting with this it wasn't this visual. It was just a bunch of terminal output and htop.

Actually NervesCloud did pretty good in letting me see the system in action. And for those who don't know. NervesCloud is the managed SaaS version of NervesHub which is an entirely open source OTA platform. It was already really powerful. We've put a lot of work into making it nicer, more polished and expanding what it can do.

So let's start by running 1 VM. We see one of the cores blip for a moment. It calms down.

10 more VMs. We see a few blips. It calms down. Essentially it all runs on a single core, no problem.

100 VMs. We finally get some christmas lights. Looks fun. It calms down pretty quick.

This is the nasty one. If you know about load averages, that's what we're planning here.

Let's add 1000 more VMs.

We see all cores go red hot. We see load average rise as all the cores have work to do and are churning through it.

We can watch devices come online on NervesCloud. While this happens.

It takes about a minute for things to calm. Then we hit it twice more to get to 3000 and that'll take a little while. I'd love to demo 5000 or 7000. It is simply not very time-convenient to do.

Let's run an update on them.
## The Nerves System

nerves_system_qemu_aarch64 is an official supported Nerves project system. If you enable it there is a task that generates a qemu command you can use to run your project firmware under qemu. If you run it on an ARM64 Linux device it will offer KVM. If you run it on Apple Silicon it will use HVF which is similarly blazing fast. Though I did not get my Mac with a terabyte of memory. I don't know about you.

If you are on another platform you can still fall back to emulating a cortex-a53. It has still been fairly snappy in our experience.

## Useful outcomes

Beyond being kind of neat. We see this as useful for isolating a Nerves device for testing. Whether that is in service of testing Nerves firmware updates or if we want to explore behavior on a slow or unreliable network. Having hardware defined in software means we can manipulate it to have problems that are rare in reality and test them properly.