
An enthusiastic developer may decide to ship a prototype IoT product on Raspberry Pi OS (once known as Raspbian). I've run Debian-based servers, they were reliable. Why wouldn't I do IoT the same?

Because it is not the same. The name of the game in embedded devices is robustness and reliability. Even in the Linux weight-class. Removing moving parts is incredibly helpful to ensure you have a device fleet with minimal surprises. While I am certain you can be diligent and really pin down a Debian device to within an inch of it's life there are *still* things it would be missing that are best practices.

I rely on Nerves for my projects and it applies a lot of well-proven practices for embedded Linux. Read-only root filesystem. Separate data partition. A/B slot deployment with health checks and automatic rollback.

Instead of being one bad .deb archive from catastrophic failure you now update your system as atomically as possible and on a bad deploy it should be reverting automatically to the known working version. All code is locked down and controlled. This also helps answer supply-chain questions about what code is currently on which device. And it helps if you want to do Secure Boot with a signed root partition.

Thankfully, if you can adopt Nerves for your application you can actually migrate to it.