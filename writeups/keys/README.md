# Keys
## Service Description
Keys is a simple service to generate public and private key pair and store a public key.

General workflow is:

1. Generate new private and public keys.
2. The service asks user to send private key again to ensure user has saved it. Then check done the service answers "OK, you have generated the key with the comment: ..."
3. You may request any public key for any user

Checksystem stores flags in comment field, so your task is to pass check in p.2.

## Vulnerability Description
#### Idea

0. The service doesn't stores the private key, but [stores its hash generated by password_hash](https://github.com/HITB-CyberWeek/hitbsecconf-ctf-2023/blob/dff4d7af44659cc319f488bea8cb60d4432bed81/services/keys/keys/generate.php#L39) and then [check it using password_verify](https://github.com/HITB-CyberWeek/hitbsecconf-ctf-2023/blob/dff4d7af44659cc319f488bea8cb60d4432bed81/services/keys/keys/check.php#L37).
1. [password_verify](https://www.php.net/manual/en/function.password-verify.php) function rely on [crypt function](https://www.php.net/manual/en/function.crypt.php). The docs says "Using the CRYPT_BLOWFISH algorithm, will result in the string parameter being truncated to a maximum length of 72 bytes".
2. So, you should find how to generate first 72 bytes of a private key.
3. When your "fake key" is done, make a request to generate.php to get a flag.

#### Generating fake private key

All private keys has the same prefix exept 3 bytes. Thats too much for brute-force via network.

The easy method to reduce number of variants to brute-force is to generate a lot of public and private keys and collect statistic of that bytes.

See files below for details.

#### Files

* [stats.php](https://github.com/HITB-CyberWeek/hitbsecconf-ctf-2023/blob/2e09796a16ab3849e84f9f8a713020959db8924e/sploits/keys/proof_of_concept/stats.php) is a script for collecting statistics
* [hack.php](https://github.com/HITB-CyberWeek/hitbsecconf-ctf-2023/blob/2e09796a16ab3849e84f9f8a713020959db8924e/sploits/keys/hack.php) is a sploit wich using such statistics.