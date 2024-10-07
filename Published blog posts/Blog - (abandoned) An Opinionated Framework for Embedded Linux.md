I have run into two types of Embedded Linux developers. On one end the kind that pick out their own set of software pieces to stitch together a bespoke stack and on the other the ones who use the [Nerves project](https://nerves-project.org) as their framework.

Nerves collects a bundle of common and particular tools necessary for doing embedded Linux and it adds its own glue to ensure a smooth end-to-end experience between these parts.

The first approach requires you to select a Linux to start. While you could use a desktop or server OS for this it is more typical to use Buildroot or Yocto. Yocto seems quite complex. Buildroot seems comparatively simple. Both of them build a custom Linux for you that is intended to work for a specific board, bring in specific software you need and all that.

Then you need to deal with partitioning, building system images, flashing them to a device. There are various ways you can do this. Some are even portable beyond Linux host machines.

Once you get to the point where you write code you are now probably capable of using C or C++ because those compilers are used to build Linux itself. But you might decide that it is too time-consuming, tricky and inefficient to develop your thing in such a low-level language. You can bring in Python, NodeJS or possibly even Java. Either way, you end up writing software in a higher level language for productivity, ergonomics and to actually ship product.

Something needs to start your application. Maybe System V-style init files or these days, systemd? If you built in Python or NodeJS you have no good multicore options so you probably want to avoid a monolithic app and rather run a few different services to share load across CPU cores.

..

