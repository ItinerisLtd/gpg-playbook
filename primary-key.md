# Primary Key

*Perform these steps on a air-gapped machine.*

Primary key is your identity which will be used for certification only - to issue subkeys that are used for encryption, signing and authentication. Primary keys should be kept offline at all times and only accessed to edit, revoke or issue new subkeys.

If you lose your primary private key, you lose your identity. Ensure you back it up securely.

## Generation

Generate a ed25519 certifying key for Tang Rufus <do-not-trust@example.test> which valid for 1 year:

```sh-session
$ gpg --expert --full-generate-key
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC and ECC
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate
Current allowed actions: Sign Certify

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? S

Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate
Current allowed actions: Certify

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
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
Key expires at Sun 10 May 17:55:39 2020 BST
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Tang Rufus
Email address: do-not-trust@example.test
Comment:
You selected this USER-ID:
    "Tang Rufus <do-not-trust@example.test>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 0xC57234F082BADD93 marked as ultimately trusted
gpg: revocation certificate stored as '/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY/openpgp-revocs.d/F5B7EF224221F45B3ECBA534C57234F082BADD93.rev'
public and secret key created and signed.

pub   ed25519/0xC57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                              Tang Rufus <do-not-trust@example.test>
```

Notes:

- Leave `Comment` empty, see: https://riseup.net/en/security/message-security/openpgp/best-practices#do-not-include-a-comment-in-your-user-id
- Use your GitHub account email, see: https://help.github.com/en/articles/associating-an-email-with-your-gpg-key

Export the primary key ID as a variable (KEYID) for use later:

```sh-session
# export KEYID=<key-id>
$ export KEYID=0xC57234F082BADD93
```

## Verifying

```sh-session
$ gpg --list-secret-keys
/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY/pubring.kbx
-------------------------------------------------------------------------
sec   ed25519/0xC57234F082BADD93 2019-05-11 [C] [expires: 2020-05-10]
      Key fingerprint = F5B7 EF22 4221 F45B 3ECB  A534 C572 34F0 82BA DD93
uid                   [ultimate] Tang Rufus <do-not-trust@example.test>
```

Understanding the output:

| Output | Meaning |
| ------ | ------- |
| `sec` | Primary key |
| `ed25519` | Key algo |
| `0xC57234F082BADD93` | Key ID |
| `2019-05-11` | Date of key generation |
| `[C]` | Capability: `Certify` only |
| `[expires: 2020-05-10]` | Expiry Date |
| `uid` | User identity |
| `[ultimate]` | Trust level: `ultimate` |
| `Tang Rufus <do-not-trust@example.test>` | The User identity |

## Revocation Certificate

> gpg: revocation certificate stored as '/var/folders/1q/gd0ns15n24lftckf69bvs7w80000gn/T/tmp.QyYdvFxY/openpgp-revocs.d/F5B7EF224221F45B3ECBA534C57234F082BADD93.rev'

Revocation certificate is generated along with the primary key pair. **You must backup this revocation certificate.**

If you forget your passphrase or if your private key is compromised or lost, this revocation certificate may be published to notify others that the public key should no longer be used. A revoked public key can still be used to verify signatures made by you in the past, but it cannot be used to encrypt future messages to you. It also does not affect your ability to decrypt messages sent to you in the past if you still do have access to the private key.

## Exporting Primary Private Key

```
# gpg --armor --output <file-name>.asc --export-secret-key $KEYID
$ gpg --armor --output my-private-keys.asc --export-secret-key $KEYID
```

**You must backup the exported private key(`my-private-keys.asc`) in offline locations.**

Tips:

- You can print a hardcopy with a tool like [Paperkey](http://www.jabberwocky.com/software/paperkey/)
- [You can splitting the primary private key in parts](https://incenp.org/notes/2015/using-an-offline-gnupg-primary-key.html)

## Checkpoint

Ensure you have all of these backed up in multiple offline locations with encryption:

- Revocation Certificate (`openpgp-revocs.d/xxxx.rev`)
- Primary Private Key (`my-private-keys.asc`)
