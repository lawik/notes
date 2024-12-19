## Bootloader

- Generate a (TODO: specifically what kind?) private and public key for boot partition signing. Let's call the private key Secure Boot Key (SBKEY) and the public (SBPUB).
- Generate RPi boot loaders and flashing firmware signed by the SBKEY:
	- Generate one signed boot loader with no permanent switches flipped for the CM4 based on `secure-recovery`, embed SBPUB for signature checking.
		- TODO: specify config
	- Generate one signed boot loader with permanent switches flipped for the CM4 based on `secure-recovery`, embed SBPUB for signature checking
		- TODO: specify config
	- Generate one signed boot loader for restoring the CM4 to stock based on `recovery`
	- Generate one signed firmware for 64 bit mass storage gadget for flashing the EMMC, based on `mass-storage-gadget64`

## Raspberry Pi CM4 Boot Explanation

The steps we will go through booting a CM4 with Secure Boot and boot partition and leveraging the trust of the initial signed boot to chain into trust for the root filesystem.

1. Secure boot loader checks for `boot.img` and `boot.sig`. It checks that `boot.sig` is the result of hashing `boot.img` and signing that hash. On success this confirms that it is a valid, trusted `boot.img`.
2. The `boot.img` gets started and contains a fundamental drivers, kernel files and fixups, `config.txt`, `cmdline.txt`, the SBPUB key and `rootfs.cpio.zst` (or other rootfs image). The `config.txt` instructs it to load `rootfs.cpio.zst` as an initramfs. So far we are only the signed and trusted code packed into the `boot.img`. We start the `init` script of the signed rootfs.
3. The `init` script needs to do a few things.
	1. Mount devices and perform some preparatory work.
	2. Extract dm-verity root hash from wherever that is stored (TODO: make a choice for RH storage) and verifying that it was created by SBKEY. We do that using the SBPUB embedded in the signed ``boot.img``.
	3. Then use dm-verity and the root hash to mount the root filesystem. This requires knowing where to get the hash tree which is either at a block offset of the device or as a file by itself.
	   Since it is generated from the root filesystem it cannot be inside the root filesystem. An offset i likely the good choice. If you put it on the boot filesystem then your boot filesystem must match your root filesystem which works for the current two-boot-partition Raspberry Pi setup of Nerves but it is probably not the best way.
	4. Optional: This is also where you would extract any encryption keys and introduce dm-crypt for decrypting an encrypted filesystem. Or on first boot potentially encrypt the filesystem. Exciting stuff. (TODO: flesh out disk encryption)
	5. Prepare anything else that `erlinit` will need.
	6. Perform a `switch_root` into the dm-verity-based root filesystem which will now be verified as it is used.
	7. `erlinit` gets to run. 
4. We are now in Nerves on signed and verified firmware. Any reading of the root filesystem will trigger signature checks on the dm-crypt hash tree as needed.

Packaging (only particularly relevant files/structures):

- my_firmware.fw
	- boot.img
		- kernel, fixup, drivers, necessities.
		- rootfs.cpio.zst
			- init
			- signing_key.pub
		- config.txt
		- cmdline.txt
	- boot.sig
	- rootfs.squashfs
	- meta.conf
		- partition table
			- Creates bootable boot partition with FAT32, adds boot.img and boot.sig to it.
			- Writes rootfs.squashfs as a root partition.

## dm-verity

A standard tool for signing a filesystem. It will perform filesystem verification as the filesystem is used instead of expensively up-front.

The primary CLI-tool is named `veritysetup`.

Extract relevant bits from:
```
 1022  truncate -s 100M hash.img

 1023  ls -calh

 1024  veritysetup format data.img hash.img

 1025  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1026  mount /dev/mapper/verity-test mnt

 1027  cat mnt/one.txt 

 1028  cat mnt/*

 1029  umount mnt

 1030  veritysetup close verity-test

 1031  mount -o loop data.img mnt/

 1032  vim mnt/one.txt 

 1033  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1034  umount mnt

 1035  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1036  veritysetup close verity-test

 1037  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1038  veritysetup close verity-test

 1039  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1040  mount /dev/mapper/verity-test mnt

 1041  ls

 1042  veritysetup close verity-test

 1043  mount -o loop data.img mnt/

 1044  vim mnt/one.txt 

 1045  umount mnt

 1046  veritysetup open data.img verity-test hash.img b94e6798bc47a967199ae438cf7e4fb9d0d2def793a1369349202ba970533dc2

 1047  clear

 1048  veritysetup -h

 1049  veritysetup format data.img hash.img

 1050  umount mnt

 1051  mount -o loop data.img mnt/

 1052  umount mnt

 1053  veritysetup close verity-test

 1054  ls mnt

 1055  ls -l

 1056  veritysetup format data.img hash.img

 1057  veritysetup format data.img data.img --data-block-size=4096 --hash-offset=134217728

 1058  ls

 1059  ls -l

 1060  veritysetup open data.img verity-test data.img 4bd8f520fbfbf76488c5d553fa78704725092a14a651c4ca8fd0a019e2246208

 1061  veritysetup open data.img verity-test data.img 4bd8f520fbfbf76488c5d553fa78704725092a14a651c4ca8fd0a019e2246208 --hash-offset=134217728

 1062  ls mnt

 1063  mount /dev/mapper/verity-test mnt

 1064  ls mnt

 1065  cat mnt/one.txt 

 1066  umount mnt

 1067  veritysetup close verity-test

 1068  cat ~/.bash_history 

 1069  cat /root/.bash_history 

 1070  cat /home/lawik/.bash_history
 ```
## Hardware prep