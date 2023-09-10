Re: Ex output, try -c ttyso in the erlinit. config to output to the UART pins.

fhunleth 1d ago

Re: arm vs arm64, it's supposed to be arm64, but if I remember right, the Pi has symlinks to arm from some places in arm64. I can't remember whether it device tree or configs. I feel like l've run into this before. I'II need to check. fhunleth 1d ago

Also, anything that modifies bem2711_defconfig won't do anything. nerves_system_rpi4 provides its own linux-6. 1. defconfig. It's similar, but stripped down by removing lots of kernel modules.

fhunleth 1d ago

One thing that I do as a first step is run lsmod on RaspberryPi OS  and save the list. Then on Nerves, I run cmd("lsmod") and see what fails to load. The reason will either be a missing device tree overlay or a module that's not included in the kernel. The latter is easy to check since you can run find . "\*.ko" to find all the kernel modules in the build directory and hence the image. The former is hard with the Raspberry Pi since the bootloader does magic. When I'm frustrated, I mount the image and manually copy every .dtbo file to the overlays directory.


