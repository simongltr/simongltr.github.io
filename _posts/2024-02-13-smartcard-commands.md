---
layout: post
title:  "PGP setup with smartcard (Yubikey)"
date:   2024-02-13
---

# Smartcard commands

This is a guide to setup a PGP key with a Yubikey and to create a backup of the keys. The guide is written for MacOS but if you take security seriously, please consider using a dedicated offline Linux machine with Tails OS (GPG commands are mostly the same).

## Preliminary steps
```
brew install gnupg pinentry-mac ykman
export EMAIL="exemple@exemple.com"
echo $EMAIL
```

## Generate pgp keys and create a backup folder

Create backup folder:
```
mkdir ~/Documents/mykeys
cd ~/Documents/mykeys
```

Create the master password and the admin pin:
```
openssl rand -base64 32 > passphrase.txt
openssl rand -base64 32 > adminpin.txt 
```

Create configuration files:
```
gpg --list-keys
echo "default-cache-ttl 60\nmax-cache-ttl 120\npinentry-program /opt/homebrew/bin/pinentry-mac" > ~/.gnupg/gpg-agent.conf
gpg-connect-agent /bye
echo "reader-port Yubico Yubi" > ~/.gnupg/scdaemon.conf
gpgconf --reload scdaemon
```

Generate the master key:
```
gpg --expert --full-generate-key

gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection? 8

Possible actions for this RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Sign Certify Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for this RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Certify Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? e

Possible actions for this RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Certify 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: simon
Email address: s.glatre@gmail.com
Comment: 
You selected this USER-ID:
    "simon <s.glatre@gmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: directory '/Users/simon/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/Users/simon/.gnupg/openpgp-revocs.d/B897195F0C977FD64539A7DCCC749C9B694007E4.rev'
public and secret key created and signed.

pub   rsa4096 2024-01-31 [C]
      B897195F0C977FD64539A7DCCC749C9B694007E4
uid                      simon <s.glatre@gmail.com>
```

Generate the subkeys:
```
gpg --expert --edit-key $EMAIL

gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 4
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
Key expires at Ven 30 jan 11:03:28 2026 CET
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 6
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
Key expires at Ven 30 jan 11:03:54 2026 CET
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 8

Possible actions for this RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? =a
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
Key expires at Ven 30 jan 11:04:34 2026 CET
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> save
```

Check the result:
```
gpg --list-public-keys
gpg --list-secret-keys
```

Backup the keys and useful public information:
```
gpg --armor --export-secret-keys $EMAIL > master.asc
gpg --gen-revoke $EMAIL > revoke.asc
gpg --armor --export-secret-subkeys $EMAIL > sub.asc
gpg --fingerprint --fingerprint $EMAIL > fingerprints.txt
gpg --armor --export $EMAIL > pub.asc
cp -r ~/.gnupg ./gnupg.backup
```

## Setup the Yubikey

Insert the Yubikey and check that it is recognized:
```
gpg --card-status
```

Transfer the subkeys to the Yubikey:
```
gpg --edit-key $EMAIL

gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> key 1

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb* rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> keytocard
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb* rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

Note: the local copy of the secret key will only be deleted with "save".
gpg> key 1

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> key 2

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb* rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> keytocard
Please select where to store the key:
   (2) Encryption key
Your selection? 2

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb* rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

Note: the local copy of the secret key will only be deleted with "save".
gpg> key 2

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> key 3

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb* rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

gpg> keytocard
Please select where to store the key:
   (3) Authentication key
Your selection? 3

sec  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
ssb* rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
[ultimate] (1). simon <s.glatre@gmail.com>

Note: the local copy of the secret key will only be deleted with "save".
gpg> save
```

Harden the Yubikey by changing the pin and the admin pin, remember that the admin pin is in `adminpin.txt`, use a memorable password for the pin:
```
gpg --change-pin

gpg: OpenPGP card no. D2760001240100000006177802720000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? q
```

Continue the hardening by setting requiering physical touch on the Yubikey when signing, encrypting or authenticating:
```
ykman openpgp info
ykman openpgp keys set-touch sig on
ykman openpgp keys set-touch enc on
ykman openpgp keys set-touch aut on
ykman openpgp info
```

## Recreate a fresh environment, with no private information

Reset gpg:
```
rm -r ~/.gnupg
gpg --list-public-keys
echo "default-cache-ttl 60\nmax-cache-ttl 120\npinentry-program /opt/homebrew/bin/pinentry-mac" > ~/.gnupg/gpg-agent.conf
gpg-connect-agent /bye
echo "reader-port Yubico Yubi" > ~/.gnupg/scdaemon.conf
gpgconf --reload scdaemon
```

Import the public key and create stubs for the private keys:
```
gpg --import pub.asc
gpg --list-public-keys
gpg --list-secret-keys
gpg --card-status
gpg --list-secret-keys
```

Trust your own key with ultimate trust using the "trust" command in gpg:
```
gpg --edit-key $EMAIL
gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: unknown       validity: unknown
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
     card-no: 0006 17780272
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
     card-no: 0006 17780272
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
     card-no: 0006 17780272
[ unknown] (1). simon <s.glatre@gmail.com>

gpg> trust
pub  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: unknown       validity: unknown
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
     card-no: 0006 17780272
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
     card-no: 0006 17780272
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
     card-no: 0006 17780272
[ unknown] (1). simon <s.glatre@gmail.com>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

pub  rsa4096/CC749C9B694007E4
     created: 2024-01-31  expires: never       usage: C   
     trust: ultimate      validity: unknown
ssb  rsa4096/8BAE7179A13643CB
     created: 2024-01-31  expires: 2026-01-30  usage: S   
     card-no: 0006 17780272
ssb  rsa4096/E4B84DE860069A6B
     created: 2024-01-31  expires: 2026-01-30  usage: E   
     card-no: 0006 17780272
ssb  rsa4096/E41FCDFD84F9D4F0
     created: 2024-01-31  expires: 2026-01-30  usage: A   
     card-no: 0006 17780272
[ unknown] (1). simon <s.glatre@gmail.com>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> save
Key not changed so no update needed.
```

## Encrypt the backup folder

Keep a copy of pub.asc in your documents, you will need it later. Optionally, publish it on a keyserver for others to find it easily.

Use the veracrpyt GUI or LUKS to create the encrypted backup. The password to encrypt the backup must be strong and new. 

Delete the `mykeys` folder when the backup is done.

## Test the setup

```
cd ~/Documents
echo "Privacy is the power to selectively reveal oneself to the world. - Eric Hughes" > message.txt
gpg --encrypt --armor --recipient $EMAIL -o encrypted.asc message.txt
gpg --decrypt encrypted.asc
gpg --armor --clearsign -o signed.asc message.txt
gpg --verify signed.asc
```

## Add SSH support

Add ssh support:
```
echo "enable-ssh-support" >> ~/.gnupg/gpg-agent.conf
gpgconf --kill gpg-agent
```

Replace ssh-agent with gpg-agent by adding to ~/.zshrc:
```
unset SSH_AGENT_PID
if [ "${gnupg_SSH_AUTH_SOCK_by:-0}" -ne $$ ]; then
  export SSH_AUTH_SOCK="$(gpgconf --list-dirs agent-ssh-socket)"
fi
export GPG_TTY=$(tty)
gpg-connect-agent updatestartuptty /bye >/dev/null
```

Enable the SSH support for your auth subkey:
```
source ~/.zshrc
gpg --list-keys --with-keygrip
echo KEYGRIP_OF_AUTH_KEY >> ~/.gnupg/sshcontrol
gpg --export-ssh-key $EMAIL
```