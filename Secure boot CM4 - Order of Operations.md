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
###### Create a hash tree for a disk image
# requires the filesystem image
# produces a root hash and an offset for the hash tree information

export FS_PATH=./data.img
# grab the size of the filesystem image as the offset for appending hash data
export DATA_SIZE=$(stat -c %s $FS_PATH)

# Generate a hash-tree for $FS_PATH and save it to $FS_PATH at the offset
# which actually means we append to the file making it larger
# save the root hash (the "key" for validating the hash tree) to a file
veritysetup format $FS_PATH $FS_PATH \
  --data-block-size=4096 \
  --hash-offset=$DATA_SIZE \
  --root-hash-file=root-hash.txt

###### Mount a disk image based on the root hash and hash tree location
# requires root hash and offset information

# grab the root hash
export ROOT_HASH=$(cat root-hash.txt)

export MAPPER_NAME="verity-test"

# verity-test is just a name or label that will be used in /dev/mapper
veritysetup open $FS_PATH $MAPPER_NAME $FS_PATH \
  $ROOT_HASH \
  --hash-offset=$DATA_SIZE

mkdir mnt
mount /dev/mapper/$MAPPER_NAME mnt

###### Un-mount and close
umount mnt
veritysetup close $MAPPER_NAME
```

## Hardware prep
