# Configuring SmartCard

Insert your SmartCard and verify it is readble by `gpg`:

```sh-session
$ gpg --card-status
Reader ...........: Yubico Yubikey 4 OTP U2F CCID
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
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

## PINs

**6-digit normal PIN** is the one you use on a day-to-day basis to use your GPG key to sign or encrypt. (Default: `123456`)

**8-digit admin PIN** is used for certain operations. It's important to change this because it is used to change the 6-digit normal PIN. (Default: `12345678`)

There is also a reset code. However, there are multiple ways to reset YubiKeys:

- https://support.yubico.com/support/solutions/articles/15000006421-resetting-the-openpgp-applet-on-your-yubikey

> If a wrong PIN has been entered three times in a row the card will be blocked. It can be unblocked with the AdminPIN.
>
> **Warning**
> It is also important to know that **entering a wrong AdminPIN three times in a row destroys(!) the card.** There is no way to unblock the card when a wrong AdminPIN has been entered three times.
>
> -- https://www.gnupg.org/howtos/card-howto/en/ch03s02.html

### Changing PINs

```sh-session
➜ gpg --card-edit

gpg/card> admin
Admin commands are allowed

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

Your selection? Q

gpg/card> Q
```

Note the `PIN changed.` messages.
