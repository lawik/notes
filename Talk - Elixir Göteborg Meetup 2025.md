
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
- This was security mechanism cold-plunge. We did signed digests, MAC or Message Authentication Code. We used most of the common building blocks of cryptography and computer security schemes.
- Let's talk fundamentals and build our way back into this.

## Fundamental 1 - Symmetric

- Symmetric encryption and decryption using a single secret key
- Efficient, works well even on larger data
- Can be very hard to break
- Very challenging to keep the single secret key protected during use

## Fundamental 2 - Assymetric

- Encryption and decryption using a secret private key and a harmless public key
- Public key can encrypt something. That can only be decrypted by the private key.
- Private key can encrypt something. It will be decrypted by the public key. We don't call it this. We'll return to it.
- The private key can be used to derive the public key, not the other way around
- Incredibly powerful mechanism
- Very inefficient for encrypting large amounts of data securely. Slow, size limits, etc. Possible but not very practical.

## Fundamental 3 - Hashing

- Turn a larger amount of data into a small amount of data that is a reliably unique enough representation of that data.
- A hash cannot be turned into the data.
- Hashes are nice for storing passwords safely. Can be verified without even having the original value in your database.

## Combining things

- Hybrid cryptography. Using the special capabilities of assymetric cryptography or related algorithms like Diffie-Hellman, to safely transfer or establish a secret for symmetric encryption.
- Signing and verifying. Usually means using assymetric cryptography to encrypt the hash of some content as trustworthy verification that it was provided by the holder of the signing key.
- Authentication. Verifying that both parties have a secret without exposing the secret. Commonly using techniques like a MAC. Message Authentication Code. That word doesn't have any other meanings in computer science.
- Now we've covered most of the parts of what we'll be using.

## The Hardware

- This thing is the Microchip ATECC608. A very affordable security chip. In the Nerves ecosystem we sometimes call it NervesKey.
- NervesKey is actually a particular configuration of these devices and the library to use that configuration.
- We use it for mutual TLS or mTLS to authenticate devices connecting to NervesCloud in a very secure and reliable manner. It can also be used to authenticate with Google's cloud or AWS for similar purposes. When doing this you delegate the initial handshake of the TLS connection, not the ongoing encryption. This chip is quite slow.
- You can actually shove a bunch of keys into this thing. The typical NervesKey config only has a device private key and an auxiliary key for special purposes.
- What I've configured here is something different.
## The Volatile Config

- I needed to store disk encryption keys.
- From reading summary data sheets and such I had seen that this chip could do AES symmetric encryption. That should be appropriate.
- The implementation is not very appropriate for most needs. It goes across an unencrypted I2C bus.
- You can secure the I2C bus a bit. If you have some shared secret between the CPU and the security chip. Now where can we store a secret key... This is not really a problem with the chip. It is a conceptual limitation.
- But the chip can be configured to only keep a secret in volatile memory. Meaning it loses the secret if the security chip 