# Signing Subkeys with SmartCards

*Perform these steps on a air-gapped machine.*

## Generation

Since YubiKey 5 doesn't supports ed25519, we settle for RSA-4096.

Generate a RSA-4096 signing subkey which valid for 1 year on your machine:

```sh-session
$ gpg --expert --edit-key $KEYID
Secret key is available.

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: ultimate      validity: ultimate
[ultimate] (1). Tang Rufus <do-not-trust@example.test>

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
Your selection? 4
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sun 10 May 18:17:54 2020 BST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: ultimate      validity: ultimate
ssb  rsa4096/0x25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
[ultimate] (1). Tang Rufus <do-not-trust@example.test>

gpg> save
```

Repeate for each SmartCard. Generate 1 signing subkey pair for each SmartCard. Each SmartCard should uses its own key pair.

## Verifying

```sh-session
$ gpg --list-secret-keys
/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY/pubring.kbx
-------------------------------------------------------------------------
sec   ed25519/0xC57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                   [ultimate] Tang Rufus <do-not-trust@example.test>
ssb   ed25519/0x81B334AF1C6FDAF7 2019-05-11 [S] [expires: 2020-05-10]
```

Understanding the output:

| Output | Meaning |
| ------ | ------- |
| `ssb` | Subkey |
| `ed25519` | Key algo |
| `0x0x81B334AF1C6FDAF7` | Key ID |
| `[S]` | Capability: `Sign` only |

## Transfering Subkeys to SmartCards

```sh-session
➜ gpg --expert --edit-key $KEYID
Secret key is available.

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: unknown       validity: unknown
ssb  rsa4096/0x25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
[ unknown] (1). Tang Rufus <do-not-trust@example.test>

gpg> key 1

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: unknown       validity: unknown
ssb* rsa4096/0x25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
[ unknown] (1). Tang Rufus <do-not-trust@example.test>

gpg> keytocard
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: unknown       validity: unknown
ssb* rsa4096/0x25ECC6B94882C6E3
     created: 2019-05-11  expires: 2020-05-10  usage: S
[ unknown] (1). Tang Rufus <do-not-trust@example.test>

gpg> Q
Save changes? (y/N) y
```

Repeat for each SmartCard.

### Verifying

Verify the subkeys have moved to YubiKey as indicated by `ssb>`:

```sh-session
$ gpg -K
/private/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY/pubring.kbx
---------------------------------------------------------------------------------
sec   ed25519/0xC57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                   [ unknown] Tang Rufus <do-not-trust@example.test>
ssb>  rsa4096/0x25ECC6B94882C6E3 2019-05-11 [S] [expires: 2020-05-10]
```
