---
layout: posts
title: "Public Key Cryptography in Bitcoin with C"
author_profile: true
last_modified_at: 2020-04-25T03:20:02-05:00
date: 2020-04-25
excerpt: ""
header:
  teaser: "/assets/images/GWpreview.png"
  og_image: "/assets/images/GWpreview.png"
  overlay_image: "/assets/images/GW190425.jpg"
  caption: "GW190425 Illustration: [Aurore Simonnet](http://auroresimonnet.com/)"
  show_overlay_excerpt: false

layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
toc: true
toc_label: "Contents"
comments: true
---

Public key cryptography is integral to the Bitcoin network. Here I explain how we can generate bitcoin keys from scratch using the C programming language, as well as some features of the Bitcoin protocol.

# Public Key (aka Asymmetric) Cryptography
The predecessor to public key cryptography is *symmetric key* cryptography, where you and I share a secret password and use it to encrypt and decrypt messages we send between one another. A fundamental flaw of this scheme is that we **must** share the secret key in order to use it, but as we are yet to establish a safe encrypted means of communication, an attacker may intercept our secret key!

Public key cryptography solves this, where each user has a secret *private key* from which a *publc key* can be generated. If I want to send you a message, I first obtain a copy of your public key and then use it to encrypt my message. Then I can transmit this encrypted message to you, where you then use your private key to decrypt the message. Notice here how you can openly share your public key rather than **secretly**, and as long as no one else knows your private key the message will be safe from prying eyes.

In the Bitcoin protocol, an *address* is where bitcoin is sent to. You can generate an address by taking a *hash* of your public key. A *cryptographic hash function* takes any input data and returns a fixed-length string, where it is impossbile to calculate the reverse as these functions are *one-way*. For example, a commonly used hash function is SHA-256:
```c
SHA256("nick")
= "7f0b629cbb9d794b3daf19fcd686a30a039b47395545394dadc0574744996a87"

SHA256("nicko")
= "17224492f2c70539c54b545f34f11a99e248c0aa200ec35b7ab66227a440e81b"
```
Notice how just adding a single letter to the input string completely changes the hash. Also see how the hash function outputs a string of fixed length regardless of input length.

## Bitcoin Addresses (legacy)
In Bitcoin there are now multiple types of addresses (see [/wiki/Address](https://en.bitcoin.it/wiki/Address)). The original "legacy" addresses implimented by Satoshi Nakamoto begin with the number `1`: `1K4Y7MF8uXFu7GrwvDcUpwEoNkKcREFnh7`. Legacy addresses are calculated using the following scheme:

With `public_key=0250863ad64a87ae8a2fe83c1af1a8403cb53f53e486d8511dad8a04887e5b2352`:
1. Calculate the SHA-256 hash of the public key 
	* `SHA256(public_key) = 0b7c28c9b7290c98d7438e70b3d3f7c848fbd7d1dc194ff83f4f7cc9b1378e98`
2. Calculate the RIPEMD-160 hash of this SHA-256 hash 
	* `RIPEMD160(SHA256(public_key)) = f54a5851e9372b87810a8e60cdd2e7cfd80b6e31`
3. Add a version byte in front of this has (`0x00` for main-net)
	* `00f54a5851e9372b87810a8e60cdd2e7cfd80b6e31`
4. To create a *checksum* we take the SHA-256 of this RIPEMD-160 hash **twice** and take the first 4 bytes. 
	* `SHA256(SHA256(RIPEMD160(SHA256(public_key)))) = c7f18fe8fcbed6396741e58ad259b5cb16b7fd7f041904147ba1dcffabf747fd`
	* `c7f18fe8` is our checksum
5. Add the checksum to the end of the original RIPEMD160 hash at step 3
	* `00f54a5851e9372b87810a8e60cdd2e7cfd80b6e31c7f18fe8`
6. Convert the byte result from hexidecimal into a [base58](https://en.bitcoin.it/wiki/Base58Check_encoding) string
	* `1PMycacnJaSqwwJqjawXBErnLsZ7RkXUAs` 

It is a complicated scheme! But it includes several very useful features which are detailed below.

## Checksum
The *checksum* allows anyone to confirm that this public address is a legitimate address. If someone sends us an address, we can verify it by performing steps 4 & 5 on the first 21 bytes, we can **check** that the last 4 bytes of the address are correct. This is most useful when entering a public address by hand where most wallets will prevent you from attempting to send bitcoin to an illegitimate address.

## Base 58
Base58 might seem like a weird way to represent addresses at first, but it has some properties that make it advantageous over a decimal 0-9 representation. The first is that using a number system with a high base allows for shorter representation of large integers. For example, the number 1337 in binary would look like `10100111001` in binary (base 2) but looks like `Q4` in base 58. As bitcoin addresses are technically just very large integers, this makes sending and reading addresses much simpler.

A computationally inclined person may ask, why not use something more natural like base64? Well base58 uses every number, lowercase letter, and upercase letter **except** for characters that can easily be mistaken (0, O, I, l). Note $$2\times26 + 10 - 4 = 58$$. Removing these confusing characters makes it much less likely for Bitcoin addresses to be entered incorrectly.

# Creating Bitcoin Addresses in C
Bitcoin uses *Elliptic curve cryptography* to create a public and private key pair. I will cover the details of how this works in a later post, but for now all we need to know is that Bitcoin uses an eliptic curve called `secp256k1` to create a public key from a private key which have the properties of asymmetric cryptography outlined above.

To use this eliptic curve in C, we will be using the [bitcoin-core/secp256k1](https://github.com/bitcoin-core/secp256k1) library, installation is [trivial](https://github.com/bitcoin-core/secp256k1#build-steps) on Linux and MacOS.

In order to use this library, we have to set up a `sep256k1_context` with
```c
#include <secp256k1.h>
static secp256k1_context *ctx = NULL;
ctx = secp256k1_context_create(
			SECP256K1_CONTEXT_SIGN | SECP256K1_CONTEXT_VERIFY);
```
From the documentation of the library: *"The purpose of context structures is to cache large precomputed data tables that are expensive to construct..."*. This allows for addresses to be generated quickly.


## Generating a Private Key
A private key in Bitcoin is a 256-bit number, also represented as 32 bytes in hexidecimal:
`E9873D79C6D87DC0FB6A5778633389F4453213303DA61F20BD67FC233AA33262`

In Unix systems, we can get randomness from `/dev/urandom`, if you `cat /dev/urandom` you will see a whole bunch of gibberish.

Using C we can load 32 random bytes by reading from `/dev/urandom`:
```c
#include <stdio.h>

int main() {
    /* Declare the private variable as a 32 byte unsigned char */
    unsigned char seckey[32];
    
    /* Load private key (seckey) from random bytes */
    FILE *frand = fopen("/dev/urandom", "r");
    
    /* Read 32 bytes from frand */
    fread(seckey, 32, 1, frand);
    
    /* Close the file */
    fclose(frand);

    
    /* Loop through and print each byte of the private key, */
    printf("Private Key: ");
    for(int i=0; i<32; i++) {
            printf("%02X", seckey[i]);
    }
    printf("\n");
}
```
Saving as `privkey.c`, compiling with `gcc privkey.c -o privkey`, and running with `./privkey`:
```
Private Key: 224877F96B66F4A114DDCE97085F5F1570EDF5EB1F1D7E6795673729A2E80B20
```

Due to limitations on the secp256k1 curve, not all private keys are valid. There is a $$1/2^{128}$$ chance that the private key is invalid, so we need to check our key is valid by including:
```c
if (!secp256k1_ec_seckey_verify(ctx, seckey)) {
	printf("Invalid secret key\n");
	return 1
}
```

## Creating a Public Key in C
Now that we have a 32 byte private key, we can use `secp256k1` to calculate the corresponding public key. First, we need to declare two new variables, a `secp256k1_pubkey` to store our public key, and a `size_t` to store the size of the public key in bytes. Uncompressed public keys are 65 bytes::
```c
secp256k1_pubkey pubkey;
size_t pk_len = 65;
```
then we serialize this public key using a function from the `secp256k1` library:
```c
/* Serialize Public Key */
secp256k1_ec_pubkey_serialize(
	ctx,
	public_key64,
	&pk_len,
	&pubkey,
	SECP256K1_EC_UNCOMPRESSED
	);
```
Note that this function takes pointers for `pk_len` and `pubkey` so that our public key.

## Creating a Bitcoin Address in C
Currently, we have a private key and its corresponding public key in bytes. We wish to follow the scheme outlined earlier to create a bitcoin address. Here we will need to use the C `crypto` library to provide the required `SHA256` and `RIPEMD160` hashing functions.

We need several new variables, a `char` variable of length 40 to store our address, a `byte` variable of length `5+RIPEMD160_DIGEST_LENGTH` which is automatically set by the `crypto` library. We also will use one other `byte` variable to store our hashes in:
```c
char pubaddress[40];
byte s[65];
byte rmd[5 + RIPEMD160_DIGEST_LENGTH];
```
First, let us duplicate our public key into `s` by looping through each byte:
```c
int j;	
for (j = 0; j < 65; j++) {
	s[j] = pubkey64[j];
}
```
Now we follow the scheme outlined earlier. We be taking the first `SHA256` hash of our public key with `SHA256(s, 65, 0)` and then take the `RIPEMD160` of this hash with `RIPEMD160(SHA_HASH, SHA256_DIGEST_LENGTH, md)` where `md` is the location where we are storing the output.

Noting that we also need to set a version byte at the start of `rmd` to `0x00` we can do this all in two lines:
```c
rmd[0] = 0;
RIPEMD160(SHA256(s, 65, 0), SHA256_DIGEST_LENGTH, rmd + 1);
```
This completes steps 1-3, now we need to find the checksum.

To create the checksum, we take the `SHA256` of this hash twice with:
```c
SHA256(SHA256(rmd, 21, 0), SHA256_DIGEST_LENGTH, 0)
```
but we only need the last 4 bytes of the checksum, so we can use `memcpy` to copy these bytes to the end of our `rmd`:
```c
memcpy(rmd + 21, SHA256(SHA256(rmd, 21, 0), SHA256_DIGEST_LENGTH, 0), 4);
```

Now we are very close to a usable bitcoin address, all that is left is to convert these bytes into base58. 


then we wish to

# Data integrity
# Data Authenticity
#




We wish to be able to create ethereum vanity addresses like `0xda66666666c...` through the only option possible, brute force. 

If you wish to start generating vanity addresses right away, you can easily do so with the [full code](https://github.com/NicholasFarrow/ethVanGen).

