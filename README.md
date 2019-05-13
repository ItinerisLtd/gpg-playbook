# GPG Playbook

## Required Readings

Read all the followings:

- https://riseup.net/en/security/message-security/openpgp/best-practices
- https://alexcabal.com/creating-the-perfect-gpg-keypair
- (YubiKey) https://github.com/drduh/YubiKey-Guide
- (WSL) https://codingnest.com/how-to-use-gpg-with-yubikey-wsl
- (SmartCard) https://www.forgesi.net/gpg-smartcard
- (Why) https://mikegerwitz.com/2012/05/a-git-horror-story-repository-integrity-with-signed-commits

## Backup

If you already have gpg setup, ensure you backed up your private keys, public keys, trusted databse and the whole `~/.gnupg` before continue.

### Passphrase

You'll be prompted to enter and verify passphrases.
Each key pair (different subkeys) can use different passphrases.
Never store your passphrases digitally!

### Expiry Date

Keep your expiry date less than a year. You can always extends key expirey date, see: https://www.g-loaded.eu/2010/11/01/change-expiration-date-gpg-key/

## Installation

You need `gpg` on both air-gapped machine and the normal machines.

Some systems install both gpg version 1 and version 2. Make sure `$ gpg --version` is showing **v2.2.15 or later**.

```sh-session
$ brew install gnupg
$ gpg --version
gpg (GnuPG) 2.2.15
libgcrypt 1.8.4
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/tangrufus/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

## Configuration

Download the recommended config from [gpg.conf](./gpg.conf). You will need this on both air-gapped machine and the normal machines.

## Generating Keys

*Perform these steps on a air-gapped machine.*

Create a temporary directory which **will be deleted on [reboot](https://en.wikipedia.org/wiki/Tmpfs)**:

```sh-session
$ export GNUPGHOME=$(mktemp -d)
$ echo $GNUPGHOME
/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY
```

Copy the recommended config to `$GNUPGHOME/gpg.conf`.

Verify the recommended config is downloaded:

```sh-session
$ cat $GNUPGHOME/gpg.conf
```

### Generating Primary Keys

*Perform these steps on a air-gapped machine.*

Follow [primary.md](./primary.md).

### Generating Signing Subkeys

*Perform these steps on a air-gapped machine.*

If you have a GPG SmartCard (e.g: YubiKey), follow [signing-subkeys-with-smart-cards.md](./signing-subkeys-with-smart-cards.md).

Otherwise, follow [signing-subkeys-without-smart-cards.md](./signing-subkeys-without-smart-cards.md).

## Exporting Public Keys

*Perform these steps on a air-gapped machine.*

**Important:** Without the public keys, you will not be able to use `gpg` to encrypt, decrypt, nor sign messages.

```sh-session
$ gpg --armor --output my-public-keys.asc --export $KEYID
```

Transfer the public keys(`my-public-keys.asc`) to the normal machines.

## Normal Machine Configuration

*Perform these steps on the nomal machines.*
*Repeat on each normal machine.*

### Prerequisites

Check `gpg` version. It should show:
- v2.2.15 or later
- `Home` is not a temporary directory, usually`~/.gnupg` or `%appdata%/gnupg`

```sh-session
$ gpg --version
gpg (GnuPG) 2.2.15
libgcrypt 1.8.4
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/tangrufus/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

Download [gpg.conf](./gpg.conf) to your gnupg `Home`, usually`~/.gnupg` or `%appdata%/gnupg`.

Update `gpg.conf` with **your primary key ID**:

```diff
- # default-key <your-user-id>
+ default-key 0xC57234F082BADD93
```

### Importing Public Keys

```sh-session
$ gpg --import /path/to/my-public-keys.asc
gpg: key C57234F082BADD93: public key "Tang Rufus <do-not-trust@example.test>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

### Trust Yourself

```sh-session
$ gpg --expert --edit-key $KEYID
gpg (GnuPG) 2.2.15; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  ed25519/C57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: unknown       validity: unknown
ssb  rsa4096/25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
     card-no: 0000 00000000
[ unknown] (1). Tang Rufus <do-not-trust@example.test>

gpg> trust
pub  ed25519/C57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: unknown       validity: unknown
ssb  rsa4096/25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
     card-no: 0000 00000000
[ unknown] (1). Tang Rufus <do-not-trust@example.test>

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

pub  ed25519/C57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: ultimate      validity: unknown
ssb  rsa4096/25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
     card-no: 0000 00000000
[ unknown] (1). Tang Rufus <do-not-trust@example.test>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> save
Key not changed so no update needed.
```

### Importing Private Subkeys (without SmartCard)

```sh-session
$ gpg --import /path/to/my-first-subkey.asc
gpg: key C57234F082BADD93: public key "Tang Rufus <do-not-trust@example.test>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

#### Verifying

```sh-session
$ gpg --list-secret-keys
/Users/tangrufus/.gnupg/pubring.kbx
-----------------------------------
sec#  ed25519/C57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                 [ unknown] Tang Rufus <do-not-trust@example.test>
ssb   ed25519/81B334AF1C6FDAF7 2019-05-11 [S] [expires: 2020-05-10]
```

Understanding the output:

| Output | Meaning |
| ------ | ------- |
| `sec#` | Primary private key is absent (Good) |
| `ssb` | Private subkey is located on the machine |

### Importing Private Subkeys (with SmartCard)

Insert you SmartCard and ensure it is readable by `gpg`:

```sh-session
➜ gpg --card-status
Reader ...........: Yubico Yubikey 4 U2F CCID
Application ID ...: D0000000000000000000000000000000
Version ..........: 2.1
Manufacturer .....: Yubico
Serial number ....: 00000000
Name of cardholder: [not set]
Language prefs ...: [not set]
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: not forced
Key attributes ...: rsa4096 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
Signature key ....: BD2E AC95 D98E 53C7 C7FE  0DE0 25EC C6B9 4882 C6E3
      created ....: 2019-05-11 17:17:30
Encryption key....: [none]
Authentication key: [none]
General key info..: sub  rsa4096/25ECC6B94882C6E3 2019-05-11 Tang Rufus <do-not-trust@example.test>
sec#  ed25519/C57234F082BADD93  created: 2019-05-11  expires: 2020-05-10
ssb>  rsa4096/25ECC6B94882C6E3  created: 2019-05-11  expires: 2020-05-10
                                card-no: 0000 000000000
```

#### Verifying

```sh-session
$ gpg --list-secret-keys
/Users/tangrufus/.gnupg/pubring.kbx
-----------------------------------
sec#  ed25519/C57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                 [ unknown] Tang Rufus <do-not-trust@example.test>
ssb>  rsa4096/25ECC6B94882C6E3 2019-05-11 [S] [expires: 2020-05-10]
```

Understanding the output:

| Output | Meaning |
| ------ | ------- |
| `sec#` | Primary private key is absent (Good) |
| `ssb>` | Private subkey is located inside the SmartCard |

### Git Configuration

Ensure you have `git` v2.21.0 or later installed:

```sh-session
$ brew install git
$ git --version
git version 2.21.0
```

Tell Git about your signing key:

```sh-session
# git config --global user.signingkey <your-signing-key-id>
$ git config --global user.signingkey 25ECC6B94882C6E3
```

Sign all commits by default in any local repository:

```sh-session
$ git config --global commit.gpgsign true.
```

(No similar options for signing tags yet, see: https://help.github.com/en/articles/signing-tags)

### Uploading GPG Public Keys to GitHub

1. Go to https://github.com/settings/keys
1. "New GPG Key"
1. Paste your public keys(`my-public-keys.asc`)

See: https://help.github.com/en/articles/adding-a-new-gpg-key-to-your-github-account

From now on, your commits will be shown as "Verified" on GitHub.
See: https://github.blog/2016-04-05-gpg-signature-verification/

Note: Git, GitHub and GitLab treat revoked/expired subkeys' signatures differently. See: https://github.com/isaacs/github/issues/1099
