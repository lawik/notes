
An enthusiastic developer may decide to ship a prototype IoT product on Raspberry Pi OS (once known as Raspbian). I've run Debian-based servers, they were reliable. Why wouldn't I do IoT the same?

Because it is not the same. The name of the game in embedded devices is robustness and reliability. Even in the Linux weight-class. Removing moving parts is incredibly helpful to ensure you have a device fleet with minimal surprises. While I am certain you can be diligent and really pin down a Debian device to within an inch of it's life there are *still* things it would be missing that are best practices.

I rely on [Nerves](https://nerves-project.org) for my projects and it applies a lot of well-proven practices for embedded Linux. Read-only root filesystem. Separate data partition. Limit writes to storage medium. A/B slot deployment with health checks and automatic rollback.

Instead of being one bad .deb archive from catastrophic failure you now update your system as atomically as possible, reboot into a new partition and on a bad deploy it will revert to the previous known working version. All code is locked down and controlled. This also helps answer supply-chain questions about what code is currently on which device. And it helps if you want to do Secure Boot with a signed root partition using tools like dm-verity.

The default Nerves setup will also not continuously write logs to the flash storage, risking corruption, wear and such fun. It instead uses a ringbuffer-based logger.

Thankfully, if you can adopt Nerves for your application you can actually migrate to it. It is not entirely trivial and the approach must be tested thoroughly on safe devices since there is a risk of hosing the disk.

The procedure I've proven out that seems to work:

Defragment the drive to try and ensure the blocks are actually at the start of the disk. e4defrag seems to do the job. A standard Raspberry Pi OS install is a single large partition [expanded to fill the drive](https://gijs-de-jong.nl/posts/raspberry-pi-file-system-resize-on-first-boot/).

Grab [this project](https://github.com/underjord/resize-to-nerves) and place the relevant files in `/boot/firmware`:

- `tryboot.txt` will be read instead of `config.txt` if you restart your device with `reboot "0 tryboot"`
- `rootfs.cpio.gz` is our initramfs. Chosen by `tryboot.txt`.
- `nerves-kernel8.img` is a kernel image that includes necessary drivers for the initramfs. Chosen by `tryboot.txt`.
- `cmdline2.txt` tells Linux to use initramfs as the root filesystem. Chosen by `tryboot.txt`.
- `config.txt` slightly modified, optional, just adds better logging. If you have a custom `config.txt` already you can definitely apply this change yourself.

Trigger the restart using `sudo reboot "0 tryboot"`.

The initramfs runs an `init` script that will attempt to shrink the filesystem and partition to a particular size, in this case `3G` (which you might want to adapt). Then it will reboot.

```
#!/bin/sh
# Init script for the initramfs, first step where we control
# things in the RPi boot sequence.

# Mount the various necessary filesystems
mount -t devtmpfs -o rw none /dev
mount -t proc proc /proc
mount -t sysfs sysfs /sys

echo "Waiting for /dev/mmcblk0p2..." > /dev/kmsg
until  [ -b "/dev/mmcblk0p2" ]; do
  sleep 5
done

echo "Getting boot size..." > /dev/kmsg
boot_size=$(lsblk -nb -o SIZE /dev/mmcblk0p1)
echo "boot partition size: $boot_size" > /dev/kmsg

echo "fscking /dev/mmcblk0p2..." > /dev/kmsg
e2fsck -f -y /dev/mmcblk0p2
echo "resize2fs down to 3G..." > /dev/kmsg
resize2fs -f /dev/mmcblk0p2 3G
echo "Resize status: $?" > /dev/kmsg
end=$((boot_size + (3*1024*1024*1024)))
echo "Shrinking partition 2 to 3G (end at $end)..." > /dev/kmsg
parted /dev/mmcblk0 ---pretend-input-tty <<EOF
resizepart
2
${end}B
Yes
quit
EOF
echo "Shrunk partition status: $?" > /dev/kmsg
echo "Resizing partition to filesystem..." > /dev/kmsg
resize2fs -f /dev/mmcblk0p2
echo "Resize status: $?" > /dev/kmsg
echo "fscking /dev/mmcblk0p2..." > /dev/kmsg
e2fsck -f -y /dev/mmcblk0p2

echo "Rebooting..." > /dev/kmsg
exec reboot -f
```

Using `parted` in this way is not recommended from documentation and the flag `---pretend-input-tty` is intended for automated tests as far as I understand. It is unfortunately the only way I've found to do an unattended shrink. I'd be very happy for any suggested improvements.

Assuming this doesn't brick the drive it should come back and you can confirm the new size with `df -h`. The neat part is that your RPi OS is otherwise untouched. Rebooting normally will not trigger the `tryboot.txt` and so on.

After this we have space in the area beyond those 3 Gigs where we could put a Nerves partition. You'd take a normal Nerves project and then modify `fwup_include/fwup-common.conf` to use an offset further out. This can be done with a temporary transitional Nerves project. Writing the new A-partition somewhere on the drive that doesn't interfere with the Raspberry Pi project. Then you can choose to either live at that larger offset forever or making a final Nerves project that overwrites the old RPi OS parts of the filesystem.

I have clients that have done this with RPi 4 devices. I know other companies have done something very similar on non-RPi devices. I've never had the procedure fail for me personally but I have had reports of failed migrations that we've not yet had a chance to investigate properly. If you need any of this. Feel free to use what I've put together but test it yourself, build up your confidence in what it does. If you need help figuring this stuff out [I do consult on this type of thing](https://underjord.io/services.html).

The reason I wrote this up was to help guide people to a migration path. I don't think it is fundamentally healthy to run a general-purpose OS setup on your embedded devices and it becomes less healthy as the venture succeeds. At scale you will have so many interesting challenges that reducing variability in your fundamental OS and updates is a massive win.

Nutritional contents of this initramfs setup:

- A buildroot setup for building the root filesystem.
- 