---
layout: posts
title: "Creating Ethereum Vanity Addresses with Python [0x0000]"
author_profile: true
image: "/assets/images/ethVanTeaser.png?123"
#header:
#image: "/assets/images/aliciaandi.jpg"


We wish to be able to create ethereum vanity addresses like `0xda66666666c3a809ADA79D93114a3662476cC0` through the only option possible, brute force.

First we will need the `ethereum` **Python 3** module which easily allows us to create a *private key* which is then used generate a *public key*, hopefully with the pattern of characters which we desire `0x1234...`.

Into your terminal type:
~~~shell
python3 -m pip install ethereum
~~~

Now in a new Python file `vanGen.py` we import the required tools. We wish to use `os.urandom()` to generate [more random than random](https://stackoverflow.com/questions/47514695/whats-the-difference-between-os-urandom-and-random) private keys just to be safe.


~~~python
from ethereum import utils
import os
privKey = utils.sha3(os.urandom(4096))
print(privKey)
~~~
Running the file in a terminal:
~~~shell
nick@nick:~/repos$ python3.7 vanGen.py
b"m\x92\xdc\x82\x06\x11\x12\xae\x13\x14'\xe3O\x83\x88\xc8\xfd\xc4\x960k\x12f\x1d\xd4\xf4\xa6\tcN\x06U"
~~~
Here we have generated a private key using a string of 4096 random bytes.
Next we convert this private key into an address in bytes `rawAddress` and then convert it into a hex checksum `accAddress`:

~~~python
rawAddress = utils.privtoaddr(privKey)
accAddress = utils.checksum_encode(rawAddress)
print(accAddress)
~~~
~~~shell
0xf42eB52C2590F7Ec32c57e7d2045E880914827ac
~~~
Nice, now let's put this all in a function:
~~~python
def createAddress():
  privKey = utils.sha3(os.urandom(4096))
  rawAddress = utils.privtoaddr(privKey)
  accAddress = utils.checksum_encode(rawAddress)
  return privKey, accAddress
~~~
Now say we wish to find an address with `n` leading `0`s:
~~~python
n = 3
while True:
  privKey, accAddress = createAddress()

  # We start from 2 to ignore 0x___
  if n*'0' == accAddress[2:2+n]:
    print("Found address: {}".format(accAddress))
    print("Private Key {}".format(privKey))
~~~

~~~shell
nick@nick:~/repos$ python3.7 vanGen.py
Found address: 0x000de1d80326F378FB030d5DF5AdE42B717aE534
Private Key b'$[\x93\xfek|\xfd\xc9\x02{\x0b\xcdeSl\xbaIu\xe7(\xcd\x02\x9bN\xadO\x81"\xc2\xee\xf0\xaf'
Found address: 0x0006b6dF8dd9F95206B621E338dBb9c7c907D836
Private Key b'\x8cq6\xe7!\xc2S\x14,\x98F\x86\xcc\x8d\xe46Y\x88MF{\xcf\xa6k\x15u\xb7\xb7\x9c\xd5YH'
~~~
It only takes seconds to find numerous addresses with this pattern!

Now if we want to have the **hex** private key rather than the bytes for easier copying into wallets or metamask we can use:
~~~python
accPrivateKey = utils.encode_hex(privKey)
print(accPrivateKey)
~~~

Now suppose we want a higher amount of consecutive digits, but to speed things up we no longer mind where the digit sequence is positioned in the address, nor what character:

~~~python
charList = [char for char in '1234567890']
n = 6
while True:
  privKey, accAddress = createAddress()

  # Wrap in loop over characters:
  for char in charList:

    # We check for 'in' rather than ==
    if n*char in accAddress:
      accPrivateKey = utils.encode_hex(privKey)
      print("Found address: {}".format(accAddress))
      print("Private Key (hex) {}".format(accPrivateKey))
~~~

~~~shell
nick@nick:~/repos$ python3.7 vanGen.py
Found address: 0x5AA832a1491016FAAd444444f4D792A73aa2a550
Private Key (hex) 64a7b90c3361d8fdfed44dc1eeee351405a0acfbcd15b71bc8c751f50ac58371
~~~
There are lots of other custom addresses you can search for. For example you can look for words which use characters `A-F` with alphanumeric words. An (old) [implimentation of this is on my gitub](https://github.com/NicholasFarrow/ethereumVanityAddressGenerator/blob/master/ethereumVanityGen.py).
