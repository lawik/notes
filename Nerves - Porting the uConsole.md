TODO: try downgrading to linux 5

```
The Linux version is specified in the `nerves_defconfig`. Copy/paste the `linux-5.15` directory from an older Nerves system and update any paths in `nerves_defconfig` to downgrade.
```

``

The Clockwork Pi uConsole is a cool device with plenty of input, output and capabilities. I wanted to try if I could get it going with Nerves. In this post I will try to capture the tricksy things that have not been documented about implementing new device support for a reasonably open device in the Raspberry Pi family.

My uConsole runs on an Raspberry Pi Compute Module 4 (CM4 for short). That's essentially a Pi 4. And as luck would have it the `nerves_system_rpi4`, specifically `circuits-quickstart` for the Pi 4 ran fine. It didn't start the display and as such I have no idea if the keyboard worked. But I could get it on wifi and do Nerves stuff on it. Good starting point.

Display and input devices where my primary concern to get going. Unfortunately I have zero experience actually dealing with linux hardware drivers at this level. Fortunately the device has a repo for the Clockwork OS, their patched Raspbian OS.

- Starting from a system that is close
- Figuring out the changes needed
- Building a custom nerves system
- Swearing a lot
- Figuring out drivers
- Figuring out defconfig
- Rebuilding, a lot
- Cache busting, a lot
- 