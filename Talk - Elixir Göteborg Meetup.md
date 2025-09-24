
## Cold open - DEMO

- This is a security chip.
- Commonly used for secure communication with cloud services.
- Useful to store secrets with a few gigantic asterisks.
- This particular one is set up in an annoyingly secure mode.
- If it loses power it will refuse to work again until you say the magic word.
- Let's plug it in.
- In this Livebook we can verify that all the common operations fail.
	- We get a handle for our i2c connection to the chip
	- We check the persistent latch. This is the single bit that tracks whether the device is authed or not.
	- Digest would be used as part of mTLS negotiation. It fails when not authenticated. This indicates that we can't use the private key hidden in this device for anything fun and cryptographic.
	- Generating a MAC with the encryption key stored in the device. I like to use this for deriving keys for various things. Disk encryption, encrypted SQLite via SQL Cipher, that kind of thing. It also won't work.
	- Then we provide the very secret password hard-coded and burned into the device.
	- Now a digest works.
	- We can generate a fun variety of MACs. The chip is usable.
- This was security mechanism cold-plunge. We did signed digests, MAC or Message Authentication Code. We used b

## 