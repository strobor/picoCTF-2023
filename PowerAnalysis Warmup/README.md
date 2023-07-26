# PowerAnalysis: Warmup

## Challenge

This encryption algorithm leaks a "bit" of data every time it does a computation. Use this to figure out the encryption key.

Download the encryption program here [encrypt.py](https://artifacts.picoctf.net/c/432/encrypt.py).

The flag will be of the format `picoCTF{<encryption key>}` where `<encryption key>` is 32 lowercase hex characters comprising the 16-byte encryption key being used by the program.

## Solution

encrypt.py consists of the following:

```
#!/usr/bin/env python3
import random, sys, time

with open("key.txt", "r") as f:
    SECRET_KEY = bytes.fromhex(f.read().strip())

Sbox = (
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16,
)

# Leaks one bit of information every operation
leak_buf = []
def leaky_aes_secret(data_byte, key_byte):
    out = Sbox[data_byte ^ key_byte]
    leak_buf.append(out & 0x01)
    return out

# Simplified version of AES with only a single encryption stage
def encrypt(plaintext, key):
    global leak_buf
    leak_buf = []
    ciphertext = [leaky_aes_secret(plaintext[i], key[i]) for i in range(16)]
    return ciphertext

# Leak the number of 1 bits in the lowest bit of every SBox output
def encrypt_and_leak(plaintext):
    ciphertext = encrypt(plaintext, SECRET_KEY)
    ciphertext = None # throw away result
    time.sleep(0.01)
    return leak_buf.count(1)

pt = input("Please provide 16 bytes of plaintext encoded as hex: ")
if len(pt) != 32:
    print("Invalid length")
    sys.exit(0)

pt = bytes.fromhex(pt)
print("leakage result:", encrypt_and_leak(pt))
```

The important portion is the `leaky_aes_secret` function. We notice that it will run 16 times, once for each byte of the key and data. The data and key byte will be XORed, which is then used as an index for `Sbox`. If the number from Sbox is odd, then the leakage result will increase by 1. Importantly, each calculation using `key_byte` or `data_byte` can contribute either only 0 or 1 to the leakage result.

One aspect of this leaky scheme that allows us too break it is that each iteration of `leak_aes_secret` is independent. Changing one byte of the data will not affect the leakage of the other bytes. So, each individual byte of the key can be found fairly quickly.

First, our breaking algorithm will run through a few possible `data_byte` values to set a leakage baseline for that byte. For example, if altering `data[0]` varies the leakage result between `15` and `16`, then we know that a leakage result of 15 corresponds to an even `Sbox` value and 16 corresponds to an odd `Sbox` value.

To determine a key byte, we begin a list of all possible indices (0-255). Then, we remove indices when they do not match the parity of the `Sbox` value gotten from XORing the data and key byte.

Setting the data byte to 0, we can now determine whether `Sbox[key_byte]` is even or odd by using the leakage result. Now we have a list of indices corresponding to either odd or even numbers in `Sbox`, which are also the potential values of the `key_byte`.

Now, we will remove indices from this list until we find the correct index. We start by now setting the `data_byte` to 1 so that the `key_byte` is XORred with 1. We check whether the resulting `Sbox` value is even or odd by considering the leakage result. Then, we test each remaining index as the `key_byte` and see if `Sbox[1 ^ key_byte]` matches the parity. If it doesn't, we remove that index from the list. We repeat this process, incrementing the `data_byte` by 1, until we only have one index remaining, which must be the `key_byte`.

A crude implementation of this algorithm has been created as `break.py`. Setting the server port and running it, we get the key and the flag.

`picoCTF{1f0841ad6d79da6bad982c41a3cf8a7f}`

As an example, here is edited output for determining the first byte of the key: `0x1f`. The leakage base has been established as `7`.

```
Sent '00000000000000000000000000000000'
leakage result: 7
indices: [1, 4, 8, 12, 15, 16, 17, 20, 23, 25, 26, 28, 29, 30, 31, 35, 36, 39, 40, 45, 48, 52, 53, 55, 57, 58, 59, 62, 66, 67, 69, 70, 71, 72, 74, 79, 82, 84, 85, 88, 90, 92, 93, 94, 96, 98, 106, 108, 109, 111, 114, 116, 118, 120, 121, 122, 124, 127, 129, 131, 134, 136, 138, 140, 144, 147, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 160, 161, 162, 163, 165, 166, 167, 168, 170, 171, 174, 177, 182, 184, 185, 186, 187, 189, 190, 191, 192, 193, 195, 196, 197, 198, 199, 200, 202, 207, 208, 209, 211, 212, 214, 215, 220, 223, 225, 226, 230, 231, 233, 236, 238, 240, 245, 246, 247, 252, 253, 255]
```

The first leakage result is 7, meaning `Sbox[0 ^ key_byte]` is even. We pruned all the indices with odd numbers.

```
Sent '01000000000000000000000000000000'
leakage result: 7
indices: [16, 17, 28, 29, 30, 31, 52, 53, 58, 59, 66, 67, 70, 71, 84, 85, 92, 93, 108, 109, 120, 121, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 160, 161, 162, 163, 166, 167, 170, 171, 184, 185, 186, 187, 190, 191, 192, 193, 196, 197, 198, 199, 208, 209, 214, 215, 230, 231, 246, 247, 252, 253]
```

Now, `Sbox[1 ^ key_byte]` is even. We now prune all the indices where `Sbox[1 ^ index]` is odd. The pattern continues in the following manner:

```
Sent '02000000000000000000000000000000'
leakage result: 7
indices: [28, 29, 30, 31, 53, 59, 71, 92, 109, 120, 148, 149, 150, 151, 152, 153, 154, 155, 160, 161, 162, 163, 167, 170, 184, 185, 186, 187, 191, 193, 196, 197, 198, 199, 209, 214, 247, 253]
Sent '03000000000000000000000000000000'
leakage result: 7
indices: [28, 29, 30, 31, 148, 149, 150, 151, 152, 153, 154, 155, 160, 161, 162, 163, 184, 185, 186, 187, 196, 197, 198, 199]
Sent '04000000000000000000000000000000'
leakage result: 8
indices: [28, 31, 149, 150, 154, 155, 160, 184, 198]
```

Since the leakage result was 8, `Sbox[4 ^ key_byte]` was odd and we removed all indices where `Sbox[4 ^ index]` was even.

```
Sent '05000000000000000000000000000000'
leakage result: 7
indices: [28, 31, 149, 150, 160, 184, 198]
Sent '06000000000000000000000000000000'
leakage result: 7
indices: [28, 31, 149, 150, 160, 184, 198]
Sent '07000000000000000000000000000000'
leakage result: 8
indices: [28, 31, 149, 150]
Sent '08000000000000000000000000000000'
leakage result: 7
indices: [28, 31, 149]
Sent '09000000000000000000000000000000'
leakage result: 8
indices: [28, 31]
Sent '0a000000000000000000000000000000'
leakage result: 8
indices: [28, 31]
Sent '0b000000000000000000000000000000'
leakage result: 7
indices: [28, 31]
Sent '0c000000000000000000000000000000'
leakage result: 8
indices: [31]
result: 0x1f
```

Ultimately, the only remaining index is 31, so the first byte of the key is `0x1f`.
