---
layout: posts
title: "Cryptography in Bitcoin: How to Create an Address with C"
author_profile: true
last_modified_at: 2020-06-17T03:20:02-05:00
date: 2020-04-25
excerpt: "How to create a legacy Bitcoin address from scratch with basic-intermediate C programming, using public key cryptography, hashing functions, and base 58."
header:
  teaser: "/assets/images/ritchie_btc_boomer.png"
  og_image: "/assets/images/ritchie_btc_boomer.png"
  image : "/assets/images/ritchie_btc_boomer2.png"
  caption: "ritchie and thompson"
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

Public key cryptography is integral to the Bitcoin network. Here I explain how we can generate Bitcoin private & public keys, as well as Bitcoin addresses from scratch using the C programming language. I also cover some design choices and features of the Bitcoin protocol implemented by Satoshi Nakamoto.

# Public Key Cryptography (Asymmetric)
The predecessor to public key cryptography is *symmetric key* cryptography, where you and I share a secret password and use it to encrypt and decrypt messages that we send eachother. Think of this like a cipher, where you might replace the letter `A` with `B`, and `B` with `C` and so on. To decrypt your secret message, I would do the reverse using the same key.

A fundamental flaw of this scheme is that we **must** share the secret key in order to use it, but as we are yet to establish a safe encrypted means of communication, an attacker may intercept our secret key!

Public key cryptography solves this, where each user has their own secret *private key* and *public key*. If I want to send you a message, I first obtain a copy of your public key and then use it to encrypt my message. Then I can transmit this encrypted message to you, where you then use your private key to decrypt the message. Notice here how you can openly share your public key rather than **secretly**, and as long as no one else knows your private key the message will be safe from prying eyes.

There are a number of different public key protocols, but in all of them you can generate a public key from a private key but never the reverse. These protocols rely on special cryptographic functions that are exclusively one way, ensuring that it is impossible to determine a private key from someone's public key.

In the Bitcoin protocol, an *address* is where bitcoin is sent to. You can generate an address by taking a *hash* of your public key. A *cryptographic hash function* takes any input data and returns a fixed-length string, where it is impossible to calculate the reverse as these functions are *one-way*. For example, a commonly used hash function is SHA-256:
```c
SHA256("nick")
= "7f0b629cbb9d794b3daf19fcd686a30a039b47395545394dadc0574744996a87"

SHA256("nicko")
= "17224492f2c70539c54b545f34f11a99e248c0aa200ec35b7ab66227a440e81b"
```
Notice how just adding a single letter to the input string completely changes the hash. Also see how the hash function outputs a string of fixed length regardless of input length.

## Bitcoin Addresses (Legacy P2PKH)
In Bitcoin there are now multiple types of addresses (see [/wiki/Address](https://en.bitcoin.it/wiki/Address)). The original "legacy" addresses implemented by Satoshi Nakamoto begin with the number `1`: `1K4Y7MF8uXFu7GrwvDcUpwEoNkKcREFnh7`. This address type is known as *Pay-to-PubkeyHash* or **P2PKH** for short.

Here are the steps required to calculate a Legacy address, I'm using `public_key=0250863ad64a87ae8a2fe83c1af1a8403cb53f53e486d8511dad8a04887e5b2352`:
1. **Calculate the SHA-256 hash of the public key**
	* `SHA256(public_key) = 0b7c28c9b7290c98d7438e70b3d3f7c848fbd7d1dc194ff83f4f7cc9b1378e98`
2. **Calculate the RIPEMD-160 hash of this SHA-256 hash**
	* `RIPEMD160(SHA256(public_key)) = f54a5851e9372b87810a8e60cdd2e7cfd80b6e31`
3. **Add a version byte in front of this to complete our address (`0x00` for main-net)**
	* `00f54a5851e9372b87810a8e60cdd2e7cfd80b6e31`
4. **To create a *checksum* we take the SHA-256 of this extended RIPEMD-160 hash *twice* and take the first 4 bytes**
	* `SHA256(SHA256(0x00 & RIPEMD160(SHA256(public_key)))) = c7f18fe8fcbed6396741e58ad259b5cb16b7fd7f041904147ba1dcffabf747fd`
	* `c7f18fe8` is our checksum
5. **Add the checksum to the end of the original RIPEMD160 hash at step 3**
	* `00f54a5851e9372b87810a8e60cdd2e7cfd80b6e31c7f18fe8`
6. **Convert the byte result from hexidecimal into a [base58](https://en.bitcoin.it/wiki/Base58Check_encoding) string**
	* `1PMycacnJaSqwwJqjawXBErnLsZ7RkXUAs`
7. **Remove any extra leading 1's. In base58 a '1' represents a value of zero and thus has no value at the front of address.** However, a leading '1' is included for each leading `00` byte.
    * Nothing needs to be done here as my address above only has a single 1, matching the single `0x00`.
	* `1PMycacnJaSqwwJqjawXBErnLsZ7RkXUAs`

It is a complicated scheme! But it includes several very useful features which are detailed below.

## Checksum
The *checksum* allows anyone to confirm that this public address is a legitimate address. If someone sends us an address, we can verify by performing steps 4 & 5 on the first 21 bytes, and then **check** that the last 4 bytes of the address are correct. This is most useful when entering a public address by hand where most wallets will prevent you from attempting to send bitcoin to an illegitimate address.

## Base 58
**1K4Y7MF8uXFu7GrwvDcUpwEoNkKcREFnh7**
Base58 might seem like a weird way to represent addresses at first, but it has some properties that make it advantageous over a decimal 0-9 representation. The first is that using a number system with a high base allows for shorter representation of large integers. For example, the number 1337 in binary would look like `10100111001` in binary (base 2) but looks like `Q4` in base 58. As bitcoin addresses are technically just very large integers, this makes sending and reading addresses much simpler.

A computationally inclined person may ask, why not use something more natural like base64? Well base58 uses every number, lowercase letter, and upercase letter **except** for characters that can easily be mistaken (0, O, I, l). Note $$2\times26 + 10 - 4 = 58$$. Removing these confusing characters makes it much less likely for Bitcoin addresses to be entered incorrectly.

# Creating Bitcoin Addresses in C
Bitcoin uses *Elliptic Curve Cryptography* to create a public and private key pair. I will cover the details of how this works in a later post, but for now all we need to know is that Bitcoin uses an eliptic curve called `secp256k1` to create a public key from a private key which have the properties of asymmetric cryptography outlined earlier.

To use this eliptic curve in C, we will be using the [bitcoin-core/secp256k1](https://github.com/bitcoin-core/secp256k1) library, installation is [trivial](https://github.com/bitcoin-core/secp256k1#build-steps) on Linux and MacOS.

In order to use this library, we have to set up a `sep256k1_context` with
```c
#include <secp256k1.h>
static secp256k1_context *ctx = NULL;
ctx = secp256k1_context_create(
			SECP256K1_CONTEXT_SIGN | SECP256K1_CONTEXT_VERIFY);
```
From the documentation of the library: *"The purpose of context structures is to cache large precomputed data tables that are expensive to construct..."*. This allows for addresses to be generated quickly and we will need a context in order to generate our keys.


## Generating a Private Key in C
A private key in Bitcoin is a 256-bit number, also represented as 32 bytes in hexidecimal:
`E9873D79C6D87DC0FB6A5778633389F4453213303DA61F20BD67FC233AA33262`

In Unix systems, we can get randomness from `/dev/urandom`, if you `cat /dev/urandom` you will see a whole bunch of gibberish.

Using C we can load 32 random bytes by reading from `/dev/urandom`:
```c
#include <secp256k1.h>
#include <stdio.h>
static secp256k1_context *ctx = NULL;

int main() {
    ctx = secp256k1_context_create(
	SECP256K1_CONTEXT_SIGN | SECP256K1_CONTEXT_VERIFY);
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

Due to limitations on the secp256k1 curve, not all private keys are valid. There is a $$1/2^{128}$$ chance that the private key is invalid, so we need to check our key is valid by including the following to our `main` function:
```c
if (!secp256k1_ec_seckey_verify(ctx, seckey)) {
	printf("Invalid secret key\n");
	return 1;
}
```

## Creating a Public Key in C
Now that we have a 32 byte private key, we can use `secp256k1` to calculate the corresponding public key. First, we need to declare a new variable, `secp256k1_pubkey`, to store our public key:
```c
secp256k1_pubkey pubkey;
```
then we create the public key using a function from the `secp256k1` library:
```c
secp256k1_ec_pubkey_create(ctx, &pubkey, seckey);
```
Note that this function takes a pointer for the location of where the public key is to be written.

We now need to *serialize* this address to translate it into bytes. We use a `size_t` variable to store the length of the address in bytes:
```c
size_t pk_len = 65;
char pk_bytes[34];

/* Serialize Public Key */
secp256k1_ec_pubkey_serialize(
	ctx,
	pk_bytes,
	&pk_len,
	&pubkey,
	SECP256K1_EC_UNCOMPRESSED
	);
```
This function takes a pointer for the length and location of the public key. It *serializes* the public key into bytes so that we can then manipulate it.

## Creating a Bitcoin Address in C
Currently, we have a private key and its corresponding public key in bytes. We wish to follow the scheme outlined earlier to create a bitcoin address. Here we will need to use the C `openssl` library (`-lcrypto`) to provide the required `SHA256` and `RIPEMD160` hashing functions.

At the top of our `.c` file we require:
```c
#include <openssl/sha.h>
#include <openssl/ripemd.h>
```

To use these hashing functions we need several new variables, a `char` variable of length 34 to store our address, a `byte` variable `rmd` of length `5+RIPEMD160_DIGEST_LENGTH` (5+20) to store our final hashes. We will also use a `byte` type variable `s` to store some temporary hashes in:
```c
char pubaddress[34];
byte s[65];
byte rmd[5 + RIPEMD160_DIGEST_LENGTH];
```
We need to define the type byte at the top of our file with `typedef unsigned char byte`.

First, let us duplicate our public key into `s` by looping through each byte. Uncompresse public keys are 65 bytes:
```c
int j;
for (j = 0; j < 65; j++) {
	s[j] = pk_bytes[j];
}
```
Following the scheme outlined in the steps to create a legacy Bitcoin Address. We be taking the first `SHA256` hash of our public key `s` with `SHA256(s, 65, 0)` and then take the `RIPEMD160` of this hash with `RIPEMD160(SHA_HASH, SHA256_DIGEST_LENGTH, md)` where `md` is the location where we are storing the output.

We also need to set a version byte at the start of the address to `0x00`:
```c
/* Set 0x00 byte for main net */
rmd[0] = 0;
RIPEMD160(SHA256(s, 65, 0), SHA256_DIGEST_LENGTH, rmd + 1);
```
This completes steps 1-3, now we need to find the checksum.

To create the checksum, we take the `SHA256` of this hash twice with:
```c
SHA256(SHA256(rmd, 21, 0), SHA256_DIGEST_LENGTH, 0)
```
but we only need the last 4 bytes of the checksum, so we can use `memcpy` to copy these bytes to the end of the first 21 bytes of `rmd`:
```c
memcpy(rmd + 21, SHA256(SHA256(rmd, 21, 0), SHA256_DIGEST_LENGTH, 0), 4);
```
To use `memcpy` we need to `#include <string.h>` at the top of our file.

Now we are very close to a usable bitcoin address, all that is left is to convert these bytes into base58.

We can use this `base58` function to convert into base 58. Placing `base58()` above our `main()` function:
```c
/* See https://en.wikipedia.org/wiki/Positional_notation#Base_conversion */
char* base58(byte *s, int s_size, char *out, int out_size) {
        static const char *base_chars = "123456789"
                "ABCDEFGHJKLMNPQRSTUVWXYZ"
                "abcdefghijkmnopqrstuvwxyz";

        byte s_cp[s_size];
        memcpy(s_cp, s, s_size);

        int c, i, n;

        out[n = out_size] = 0;
        while (n--) {
                for (c = i = 0; i < s_size; i++) {
                        c = c * 256 + s_cp[i];
                        s_cp[i] = c / 58;
                        c %= 58;
                }
                out[n] = base_chars[c];
        }

        return out;
}
```

To store our address we need a `char` 34 characters long. We can now create our bitcoin address with these final lines in `main`:
```c
char address[34];
base58(rmd, 25, address, 34);
```

Finally we need to remove any extra leading 1's from the beginning of the address. We can find the number of leading `1`s we need to remove by counting the number of `1`s at the start of the address and subtracting by the number of `0x00` bytes at the beginning of our `rmd` hash. If a digit needs to be removed, we shift the memory storing the address accross by one, removing the first digit. We also shorten the string by the number of `1`s removed.
```c
/* Count the number of extra 1s at the beginning of the address */
int k;
for (k = 1; out[k] == '1'; k++);

/* Count the number of extra leading 0x00 bytes */
int n;
for (n = 1; rmd[n] == 0x00; n++);

/* Remove k-n leading 1's from the address */
memmove(address, address + (k-n), 34-(k-n));
address[34-(k-n)] = '\0';

printf("Address: %s\n\n", address);
```

When you compile with `gcc file.c -o btcGen -lcrypto -lsecp256k1`, and run with `./btcGen` **hopefully** you will be greeted with a Bitcoin address starting with `1`:
```sh
$ gcc btc.c -o btcGen -lcrypto -lsecp256k1
$ ./btcGen
Private Key: C616D1713C3BBE0627CEBBA2527C31E3304381D88BD470B27BE1318AFC3F1469
Address: 1BriRDxtB2C3e3GzxXP6PtMNCWxBeXNv33
```

# Wallet Import Format (WIF)
Great now we have an address that we can send bitcoin to! But how do we spend it? Most software wallets **WILL NOT ACCEPT** raw byte private keys. Instead, the convention is to use *Wallet Import Format*; which like in bitcoin addresses, makes the private key easier to copy and transmit.

**NEVER STORE LARGE AMOUNTS OF BTC ON SELF GENERATED WALLETS**

To convert your private key into WIF:
1. Add a version byte (0x80 for mainnet) to the front of the private key
2. Calculate the SHA256 hash
3. Calculate the SHA256 hash again
4. Take the first 4 bytes from this hash, this is our checksum
5. Add these 4 bytes to the end of the extended private key in step 1.
6. Convert to base58 (you can reuse the function from earlier)

🧠 I will leave this as an excercise for now, as it is almost identical to the bitcoin address creation scheme. If everything goes right then your WIF private key should begin with a `5`. For more details see [wiki/Wallet_import_format](https://en.bitcoin.it/wiki/Wallet_import_format).

In the Electrum/Electron wallets you can load your WIF like this:
![Wallet demo](/assets/images/electroncash.png)
(I'm only using BCH here because the low fees are nice for testing 😷)

# Bitcoin Vanity Address
By extending the above code, you can rapidly generate new bitcoin addresses in the hope of randomly reciving a 'nice' address like `1DEADBEEFx24sa...` or `100000ae...`. To see an example of this see my attempt on Github: [niceBit](https://github.com/nickfarrow/niceBit).

A rough summary of the code from this tutorial here [/assets/btc_gen.c](/assets/btc_gen.c), which is ready to compile and run.

If you found this useful, please support me in creating these tutorials:

**BTC: 1K4Y7MF8uXFu7GrwvDcUpwEoNkKcREFnh7**
