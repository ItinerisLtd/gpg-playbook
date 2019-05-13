# Signing Subkey Key without SmartCards

*Perform these steps on a air-gapped machine.*

## Generation

Generate a ed25519 singing subkey which valid for 1 year:

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
Your selection? 10
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sun 10 May 17:59:36 2020 BST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/0xC57234F082BADD93
     created: 2019-05-11  expires: 2020-05-10  usage: C
     trust: ultimate      validity: ultimate
ssb  ed25519/0x81B334AF1C6FDAF7
     created: 2019-05-11  expires: 2020-05-10  usage: S
[ultimate] (1). Tang Rufus <do-not-trust@example.test>

gpg> save
```

Repeate if you have more than 1 machine. Generate 1 signing subkey pair for each machine. Each machine should uses its own key pair.

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

## Exporting Private Subkeys from `$TMP` / `$TMPDIR`

```
# gpg --armor --output <file-name>.asc --export-secret-keys <subkey-id>!
$ gpg --armor --output my-first-subkey.asc --export-secret-keys 0x81B334AF1C6FDAF7!
```

Transfer the private subkey(`my-first-subkey.asc`) to the targed machine securely.

Repeat for each subkey.

## Exporting Public Keys

**Important:** Without importing the public key, you will not be able to use GPG to encrypt, decrypt, nor sign messages.

## Checkpoint

- Ensure you have transfered each private subkey to the normal machines
