

## Learning to use Linux

I was a teenager in a lovely part of nowhere at all in northern Sweden when I wanted to try Linux. I had been doing my PHP & MySQL hobby web dev on Windows but I knew if I wanted to set up servers, without a budget, I would need to learn Linux. And it seemed the cool thing to do.

I didn't know anything about other operating systems so it seemed completely intractable. I had no idea where to start.

Actually, not true. I had one idea where to start. There was a guy who every so often would throw technical nuggets at me. When I was doing Perl he threw PHP my way. Game changer. When I didn't have a database he threw MySQL my way. Absolute paradigm shift. He'd eventually even show me jQuery.

At this time, I knew he'd know all about Linux. So I asked for help. He pointed me in the right direction. I wanted Slackware because the name sounded cool. I burned my discs and followed the instructions and got it mostly set up.

Then he came over for a couple of days where he showed me how the damned thing worked and we tried to get both sound and networking to work at the same time. This was early 2000s Linux. AC97 audio and an ISA networking card are burned into my mind. But I learned how to get around the command-line, the overall UNIX and Linux stuff. We looked at some BSDs. I got Apache and MySQL. And so much more. It was my year of the Linux desktop.

He is here today, which is weirdly full-circle and quite heart-warming. So thank you. You know who you are.

## Learning to manage Linux

As I got my chance as a professional developer I've also always done some level of operations, or server admin as it was called. I didn't know I did ops. DevOps hadn't been coined yet. I just needed the stuff I wrote to run so I set up the server and kept it up and running.

I've operated more bespoke Linux server than I've operated container runtimes. But I've done a bit of everything.

I very rarely needed to do anything that directly touches the kernel. While I was usually one of the most Linux-experienced at my first few places of work it was not all that deep. Not to discount my efforts, I mostly just didn't need to go that deep and I knew there were depths I didn't go.

## Learning to build a Linux

Modern web and backend development typically takes you further away from the Linux underpinnings of your application. Even though it almost invariably runs on Linux. Since about the time I started working with Elixir the only times I scratched those depths was when I played with Nerves. I occasionally needed to customize something. And you are just one nice but lean layer of abstraction away from Linux with Nerves.

In the last few years as I got more into Nerves and eventually picked up clients working with that I ended up twisting and turning Nerves systems a lot more frequently. I developed some secure boot implementation stuff for the RPi4 which made me learn a lot about initramfs and how Linux starts up. It made me much more comfortable compiling my Linux kernels and building my root filesystems to make a working system that did something new.

I've found and integrated kernel patches, I've contributed to Buildroot, I've helped test and fix hardware support for various hardware with Nerves. Small things in the grand scheme of things but a different level to operate at for me.

I didn't learn this on my own either. I got a lot of help from Frank Hunleth, Jon Carstens, Connor Rigby and Alex McLain when it came to Nerves, Linux and Buildroot. And the long tail of people who've helped me in Nerves is massive.

I feel like I'm increasingly learning Linux itself and not just using or administrating a particular distribution. I've really enjoyed the process.

This talk is about what I've been up to most recently and what boundaries I've been pushing.

## The collab

The work underpinning this talk could not have been done without the sponsor pairing of GleSYS and Ampere. When I chatted with GleSYS about their sponsorship of the conference they wanted to know what might be interesting to Elixir devs specifically. We talked a bit of AI and machine learning but then they mentioned they were increasingly offering Ampere-based servers. I've heard about Ampere, the ARM servers, and I've found that fascinating. I knew the recent ones have hilarious numbers of cores. And Elixir devs like having lots of cores, because or VM lets us use them. So Claes at GleSYS suggested we talk to Ampere and now they are here, co-sponsoring the event.

I was going to give a talk here, I knew that. I hadn't decided on what. So I asked if I could borrow an Ampere One 192-core machine to do something with. They graciously agreed. Because I'm currently quite focused on Nerves, that's what I wanted to explore.

My first thought was of course.. how does the BEAM run on 192 cores. Concurrency and all that. I have some notes on that. But I had a more interesting angle. This is an ARM64 device. What do I do with ARM64 devices? I put Nerves on em. And while it would fun to treat this as a single massively overbuilt Raspberry Pi. It would also be ridiculous and not particularly useful.

There is another upside to it being an ARM server. It should virtualize ARM64 very efficiently. So the question became: How many virtual Nerves devices could I run on this thing? And could I update them with NervesCloud, our update service for embedded devices?

So thanks to Ampere and GleSYS for getting me access to the Ampere One. Let's see what I could do with it.

## What it takes

Virtual machines. Creating software-based computers inside your computer might seem really inefficient. But they have some very practical uses and a lot of work has been put into making them an efficient, useful reality.

Essentially I knew I'd need to build a new Nerves system targeting the virtual environment as if it was a new little physical Nerves board. So what does this entail? The BEAM-level parts of Nerves do not care a lot about what it runs on. So essentially we only need to lay the groundwork of making sure our version of Linux is built with drivers for the virtual hardware. And we need a bootloader.

First I needed to confirm what virtualization I should use to know what we were trying to target. I fairly quickly found qemu as the most reasonable starting point. It is a command-line tool that you can use to run a virtual computer. It can either boot Linux for you, skipping the need for a boot-loader or start the boot-loader of your choice. We'll revisit that. You provide a disk image, just a file, that it will use as the virtual computer's physical disk. You can specify the number of cores, the amount of memory available and more.

The physical computer is called the host. The virtual computer is called the guest.

Because qemu exists on ARM64 and has support for ARM64 as a guest we should get something reasonably efficient. Or at least in theory.

My first experiments with qemu was using just Buildroot which can build Linux and a root filesystem for you. It is one of the underpinnings of Nerves. But I started out testing it separately. With this I could confirm that an emulated ARM Cortex A53 worked. I could get to a Linux root shell.

(EXAMPLE command-line)

I looked up Linux virtualization support and found KVM. Kernel-based Virtual Machine. It is support in Linux for running virtual machines with low overhead. Can you use qemu with KVM? Yes. Turning it on was as easy as adding accel=kvm and changing the CPU type to `host`.

(EXAMPLE command-line with KVM)

Starts faster. Performs better and drops about 500 Mb of memory usage as we don't have to form a fictional computer from whole cloth. Essentially we are running our guest OS in isolated memory regions on the host computer. We've removed a lot of work from qemu.

If you are using an x86 machine, like a typical Intel or AMD you would not be able to use KVM to provide a virtual ARM64 machine. You'd rely on emulating it.

This was all surprisingly straight-forward. Moving the correct config over to my Nerves system and removing the unnecessary stuff and then fixing all the stuff I screwed up.. that took more work.

And I was also looking at a boot loader.

## Boot loaders and Nerves

I could actually launch Nerves on qemu without a boot loader. Qemu knows about Linux and you can just pass it a kernel. It worked perfectly fine. But a lot of the power of Nerves is in how it protects you from screwing up updates and I can't really shill our service, NervesCloud, without doing updates. And what determines if the device boots partition A or B? The bootloader.

Running without a bootloader would not simulate a full Nerves device. And I needed that.

Typical production Nerves systems use u-boot. A tried-and-true bootloader popular for embedded devices. Raspberry Pi are very different, let's not go there.

So I started fighting u-boot and I struggled. After this whole procedure I feel like I understand the basics of u-boot but we didn't end up using it and I still don't know it well. I hope to practice it more.

Just like when I was learning Linux I called on a friend. And just like in my teens it was the right call. I asked Frank Hunleth for help. I think it is a sign of maturity that I actually reached out to him before I ever got the server and said "I will really be strapped for time in making this work. Can I depend on you to unblock me and bail me out?" He agreed. And delivered. Both in unblocking me and in the strangest help I could have hoped for.

Frank and I are wired differently but in several ways we find common interest. We definitely both like some novelty and challenge. Making u-boot work would not have been interesting or difficult for him. So while I was trying to make u-boot work...

He. Built. A. Bootloader.

It is called little loader. It is quite simple he tells me. I helped test it. I have read a bunch of the code. I found fun compilation bugs with it. We had a lot of very peculiar types of fun.

Is it strictly better than u-boot? Unlikely. Is it more understandable? Almost certainly.

But in the end it worked. A bespoke boot loader built on Nerves conventions but potentially easy to adapt to any qemu usage.

It is actually passed in instead of passing the kernel to qemu, so it is a bit unusual. It doesn't do all that a regular boot loader might do in that regard.

(EXAMPLE of typical qemu call with bootloader vs little loader)

## Bundle of Nerves

We had the boot loader working only with cortex-a53 emulation at first due to some misunderstandings around ARM Exception Levels.

At this point I ran some tests. We ran 500 Nerves devices. I made a blog post.

(TODO: let the demo run either KVM+host or cortex-a53, show memory usage)

500 wasn't me hitting a limit. It was a fairly arbitrary test of running more than 1 per core. It worked. I was happy. And the next day I hoped we had KVM working.

We did. This dropped memory usage from more than 500 megabyte per VM to very close to the assigned memory allocation. I think at this point a single machine needed about 170 Mb memory to boot which wasn't ideal. We only have a single terabyte of memory!

We could easily run a couple of thousand devices though. But there were still some pretty low-hanging fruit to shrink memory usage.

We did a few things to tweak memory usage. The erlang scheduler options should reduce fragmentation and consequently total memory usage:

(TODO: example of options)

I tweaked Linux memory configs:

(TODO: systemctl.conf)

We added Linux `zram` memory compression.

(TODO: zram changes)

## Memory usage, the limit

At this point I could have the VMs start with 110Mb of RAM. There is some amount of overhead on qemu and whatever else ends up happening so I saw this get us to around 5000 devices without issue.

Then I started looking at whether we could do something about all the redundant memory we had.

See we are starting 5000 identical Linux kernels with 5000 identical operating systems that will load 5000 Erlang VMs to load 5000 copies of identical BEAM files. There will be slight differences in many places but there will be a lot of identical data in memory. Maybe the host can do something with this?

The answer I've tried. KSM. Kernel Samepage Merging. It takes the host half an eternity to find all the pages to deduplicate. But it does reclaim a lot of memory as it keeps going through. Of course you are brutally oversubscribing your actual resources so if things start to divert a lot you'll find out real quick as the Out Of Memory Killer starts rampaging across your VMs bringing them down brutally.

With this I hit 7000 devices. And I had them all connecting to NervesCloud. Then our alarms went off, so I'll stick to showing 5000 for now. I think we could get to 8000 or 9000 on the server if they do literally nothing. NervesCloud could handle a lot more. But we'd need to scale up some nodes.
## CPU

A note on CPU. We know of production Nerves devices running at around 200 megahertz. With 5000 devices the devices have essentially a 125MHz slice of the CPU. But presumably it could boost to 3.2GHz if qemu, KVM and Linux is smart enough. The challenges are mostly seen when all the devices are doing work.

I tend to start them in batches to avoid ridiculously high load numbers. But I've never seen the server even get particularly unresponsive. I don't think 5000 devices would be very happy if they were doing heavy work. It would be slow and I imagine CPU caches would get very sad and all that. For a more reasonable, lower, number of VMs it should be very nice and snappy.

## Let's try it!

So this is a frontend I published for pulling the various levers. When I was experimenting with this it wasn't this visual. It was just a bunch of terminal output and htop.

Actually NervesCloud did pretty good in letting me see the system in action. And for those who don't know. NervesCloud is the managed SaaS version of NervesHub which is entirely open source. It was already really powerful. We've put a lot of work into making it nicer, more polished and expanding what it can do.
## The Nerves System

nerves_system_qemu_aarch64 is an official supported Nerves project system. If you enable it there is a task that generate a qemu command you can use to run your project firmware under qemu. If you run it on an ARM64 Linux device it will offer KVM. If you run it on Apple Silicon it will use HVF which is similarly blazing fast. Though I did not get my Mac with a terabyte of memory. I don't know about you.

If you are on another platform you can still fall back to emulating a cortex-a53. It has still been fairly snappy in our experience.

## The BEAM on 192 cores