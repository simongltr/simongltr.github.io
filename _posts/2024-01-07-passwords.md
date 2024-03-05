---
layout: post
title:  "Passwords: how to generate entropy"
date:   2024-01-07
---

# How to generate entropy

## Introduction

### Entropy

In the context of secrets generation, entropy is a measurement of how un-guessable a secret is. Entropy is measured in bits, and the more bits of entropy a secret has, the harder it is to break it through brute force.

A secret of length `L` written using an alphabet of size `R` has `E = L * log2(R)` bits of entropy. For example, a password of length 8 written using an alphabet of size 26 (lowercase letters) has `E = 8 * log2(26) = 37.6` bits of entropy.

Using passwords like `pr0t3cT3d` or `P@ssw0rd` seems like a good idea for many people, but they are actually very weak. Longer passwords are harder to guess, but hard to remember which leads to people writing them down, reuse them or use obvious patterns.

![alt text](/assets/medias/2024-01-07/billburr.png)

### Humans are bad at generating entropy

Please do [this experiment](https://people.ischool.berkeley.edu/~nick/aaronson-oracle/) to convince yourself that generating your own entropy without method is a bad idea. I'm personally unable to have a better score than 65%, meaning that I clearly suck at generating entropy because I'm predictable more than 65% on the time. And so are you ... probably.

I will document here some methods to generate entropy that are easy to use and that will help you to generate strong and rememberable secrets.

![alt text](/assets/medias/2024-01-07/xkcd.png)


## Generating entropy

Modern operating systems have physical ways to generate entropy, and randomness with crypto secure pseudo random number generators (CSPRNG). On linux for instance, reading from `/dev/random` or `/dev/urandom` will give you random bytes coming from a CSPRNG seeded with the entropy pool of the kernel. Gathering entropy from physical sources is slow and `/dev/random` will block until enough entropy is gathered, `/dev/urandom` will never block. The crypto community is confident that `/dev/urandom` is secure enough for most use cases, this is the entropy source chosen by most security oriented software like GPG or OpenSSL.

### Non rememberable secrets

It's recommended to use high grade software when dealing with passwords generation, one of them is openssl. The following command will generate a 32 bytes (256 bits) password using a CSPRNG seeded with the entropy pool of the kernel, and will encode it in base64 to make it easier to copy/paste.

```bash
openssl rand -base64 32
eimZNc0ZRwNWZgJQFggSfyjFSjBn3Rqw6jDcY6+RjSA=
```

The following command will directly use `/dev/urandom` to generate a 32 bytes (256 bits) written with the standard alphabet (a-z, A-Z, 0-9).

```bash
cat /dev/urandom | LC_CTYPE=en.US.UTF-8 tr -dc a-zA-Z0-9 | fold -b -w 32 | head -n 1
nTVlquVGrD3oXMA5DyLxm8LSyF5rOCF4
```

Of course this kind of password is not easy to remember, but can easily be stored in a password manager.

### Rememberable passphrase

#### Using dices

Dices are a good source of entropy, and can be used to generate strong passwords. This method is 100% offline and don't rely on any computer or software. It's recommended to use this technique for generating high entropy passwords that you will need to remember, such as the master password of your password manager or the passphrase of your PGP key.

The idea is to roll dices and convert the result into a list of words using a wordlist. The wordlists I recommend come from the [EFF](https://www.eff.org/dice).

| Wordlist | Number of words | Entropy per word | Printable version |
| -------- | --------------- | ---------------- | ----------------- |
| [EFF's Long Wordlist](/assets/medias/2024-01-07/eff_large_wordlist.txt) | 7776 | 12.9 bits | [printable](/assets/medias/2024-01-07/eff_large_wordlist.pdf) |
| [EFF's Short Wordlist 1](/assets/medias/2024-01-07/eff_short_wordlist_1.txt) | 1296 | 10.3 bits | [printable](/assets/medias/2024-01-07/eff_short_wordlist_1.pdf) |
| [EFF's Short Wordlist 2](/assets/medias/2024-01-07/eff_short_wordlist_2_0.txt) | 1296 | 10.3 bits | [printable](/assets/medias/2024-01-07/eff_short_wordlist_2.pdf) |

The difference between `Short 1` and `Short 2` is the length of the words. In both cases each word encode 10.3 bits of entropy, but having longer words can help some people memorise the passphrase. I personally prefer `Short 1`, or `Long` when I need more entropy.

Quantify the entropy:

| Words | EFF's Long Wordlist | EFF's Short Wordlist 1 and 2 |
| --- | --- | --- |
|     4 |  51.70 |  41.36 |
|     5 |  64.62 |  51.70 |
|     6 |  77.55 |  62.04 |
|     7 |  90.47 |  72.38 |
|     8 | 103.40 |  82.72 |
|     9 | 116.32 |  93.06 |
|    10 | 129.25 | 103.40 |
|    11 | 142.17 | 113.74 |
|    12 | 155.10 | 124.08 |

**How to do it:**
1. Gather a pen, paper, one or more dice, and a word list. Ensure privacy by closing windows and shutters and using a pen and paper instead of a computer.
2. Roll the dices and write down the result on the paper. If you are using multiple dices, read the result from left to right to make sure your human bias don't impact the entropy.
3. Repeat step 3 until you have enough words to reach the desired entropy. The large wordlist requires 5 dice rolls per word, the short wordlists require 4 dice rolls per word.
4. Convert the dice rolls into words using the wordlist.
5. Depending on the context, you can memorise it, encrypt it using a dedicated 100% offline machine or save it into a password manager. Make sure you understand what scenario is best suited for you.
6. BURN THE PAPER and make sure you left no trace of your work behind you.

**Example:**

I want to generate a passphrase with at least 75 bits of entropy using the EFF's short wordlist 1. I will need 6 words if I choose the long wordlist, or 8 words if I choose the short wordlist 1 or 2. I choose the long wordlist.

My dice rolls are:
`4 5 2 4 5 4 6 4 4 3 6 2 3 6 5 1 1 4 1 5 2 4 2 3 5 3 6 2 3 3`

I group them by 5 and convert them into words using the wordlist:

```
45245 -> prepaid
46443 -> reach
62365 -> tiptoeing
11415 -> algebra
24235 -> dripping
36233 -> lint
```

My passphrase is `prepaid reach tiptoeing algebra dripping lint`.

I remember it by creating a story in my head that should be short and memorable.

> I'm prepaid to reach the top of the mountain by tiptoeing, where I will work on hard algebra problems making my brain dripping. I will remove the lint from my clothes before going back home.

If the words you get don't inspire you then try again but never choose words manually to improve your story. 

#### Using a computer

Given two arguments `N` and `WORDLIST`, the following command will generate a passphrase with `N` words using the wordlist at path `WORDLIST`. The wordlist is one of the EFF's wordlists, see [Using dices](#using-dices) for more details. The source of randomness is `/dev/urandom`.

```bash
WORDLIST=path/to/wordlist.txt; N=6; shuf --random-source=/dev/urandom -r -n $N $WORDLIST | awk '{print $2}' | tr '\n' ' '; echo
thank afloat shorthand staple paparazzi bagginess
```