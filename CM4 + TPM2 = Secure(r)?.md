References:

https://trustedcomputinggroup.org/wp-content/uploads/TCG-TPM-v2.0-Provisioning-Guidance-Published-v1r1.pdf
Clarifying on PCRs: https://www.reddit.com/r/linuxquestions/comments/q8kgd4/securing_the_tpm_read_access/
TPM2 and Linux: https://www.kernel.org/doc/html/next/security/tpm/tpm-security.html
https://community.infineon.com/t5/Blogs/Sealing-and-unsealing-data-in-TPM/ba-p/465547
https://community.infineon.com/t5/Blogs/Storing-and-reporting-system-measurements-with-TPM/ba-p/443590?emcs_t=S2h8ZW1haWx8dG9waWNfc3Vic2NyaXB0aW9ufExJMlVTWVZKWVhKMDRRfDQ0MzU5MHxTVUJTQ1JJUFRJT05TfGhL


Assuming we can do the Linux null primary seed bla-bla-bla thing to ensure we detect if an interposer is present and bail if so, then we can proceed and try to securely boot.

Goals:
- Make sure we can use the TPM to authenticate the device for TLS workloads.
- Make sure we can hide secrets in the TPM to access later, such as symmetric encryption keys or similar passwords for disk encryption and database encryption.
- Ensure we are running with our TPM, with our CM4/SoC and unmodified code.

# Provisioning

## Locking the CM4

In a RPi CM4 we can lock a public key/certificate into the CM4 along with the Secure Boot boot loader. This means the boot.img must be paired with a boot.sig signed by a private key that is not inside of the system. This is the first fundamental step in securing the CM4 boot chain.

This will fail to boot if:

- Anything within the boot.img is modified and the boot.sig is refreshed using the private key that is held by us, off device.

This boots into the Linux kernel. It runs our init code.


## Locking down the TPM

boot_tpm.sig as well?

# init

Our init code is responsible for mounting things, switching into the right rootfs. It will also be responsible for setting up dm-verity and dm-crypt. These require secrets.

At this stage we should re-verify our situation. The code that is running needs to check that it is actually running a properly signed boot.img by checking boot.sig. The bootloader checked this but we cannot know that the bootloader checked this. We can't know we are on the right device. But we can verify that the