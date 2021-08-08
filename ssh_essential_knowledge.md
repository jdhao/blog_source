---
title: "Essential Knowledge about SSH"
date: 2019-10-27 22:43:12+0800
tags: [ssh]
categories: [technique]
---

Although I use SSH (secure shell) every day, I seem to know little about it
except the fact that I can use `ssh` command to log into my remote servers.
Recently, I found myself some time to learn how does it work.

<!--more-->

# Preliminary knowledge

## Fingerprint

In computing, a
[fingerprint](https://en.wikipedia.org/wiki/Public_key_fingerprint) is a
shorter sequence representation of a longer message which can be used to
identified the original message. For example, in SSH, when you connect to the
server, you will get the server public key fingerprint. Then you can compare
the fingerprint you have received with the real server fingerprint (e.g., you
may have obtained the server public key fingerprint by contacting the
administrator) to make sure that you are not being attacked by hackers and
connecting the wrong server.

## Cryptographic hash function

To produce such a fingerprint and ensure security, we need to use
[cryptographic hash
functions](https://en.wikipedia.org/wiki/Cryptographic_hash_function). A
cryptographic hash functions has good properties. It makes sure that same
message always produce the same output and different messages produce different
output. It also makes sure that you can not deduce the original message from
the output. These good properties make it suitable for authentification
purposes. You may have probably used it before when you check the integrity of
files using [`md5sum`](https://en.wikipedia.org/wiki/Md5sum) or similar tools.

`MD5` and `SHA`[^3] are different hash function algorithms to generate a
fingerprint. The complete list of hash functions can be found
[here](https://en.wikipedia.org/wiki/Cryptographic_hash_function#Cryptographic_hash_algorithms).

# SSH to a server

Two ways to log in a server when using SSH:

+ use your user name and password
+ use ssh public key authentification

## Server public key verification

When we first log in a server, we will be prompted that the authenticity of the
server can not established, and we are shown the fingerprint of the server's
public key. The typical message is like the following:

```
The authenticity of host 'xxx.xx.xx.xx (xx.xx.xx.xx)' can't be established.
ECDSA key fingerprint is SHA256:zJsEufeAsutfxsZH990Sq7asIBnJvz6B9N63g0/Rx5w.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

In the above message, `ECDSA` is a type of signature algorithm used to verify
the identity of the two sides of a communication. Other signature algorithms
include `DSA`, `RSA` and `ED25519`.

To make sure that the public key of the server is true, you have to check via
other methods. For example, if you have access to the server in other more
secure ways, you can check the fingerprint of the server's ECDSA public key[^1]
with SHA hash function using the following command:

```
ssh-keygen -lf -E sha256 /etc/ssh/ssh_host_ecdsa_key.pub
```

If you want to check the fingerprint of the server in MD5 format, use the
following command instead:

```
# MD5
ssh-keygen -E md5 -lf <fileName>
```

Then you can compare the fingerprint of the server public key against the
fingerprint you have received. If the two fingerprints match, you are actually
logging to the right server. Otherwise, you may be under attack by hacker (see
[man-in-the-middle-attack
](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)).

If you have verified the identity of the new server and choose to connect, the
server's IP and public key will be stored in the file `.ssh/known_hosts`[^2]
under your HOME directory. The next time you connect to the server, the
server's public key will be compared with the public key stored in
`known_hosts` to make sure that your connection to the server is secure.

Usually you will encounter the unauthorized warning the first time you log in a
server. If you encounter the warning the second time, it may be that the server
has changed its public key or you are under attack.

## Set up public key login

As stated earlier, we can also set up public key login. First, we need to
generate a private and public key pair using `ssh-keygen`. We can use `-t` to
specify the type of key to create, e.g., `dsa`, `ecdsa`, `rsa`. We can use the
following command to generate a ecdsa key pair:

```
ssh-keygen -t ecdsa
```

It will prompt you to enter a key file name and the passphrase for the private
key. You can press Enter to use the default values. The key pair will be
created under `$HOME/.ssh`.  The private key file is `id_ecdsa` and the public
key file is `id_ecdsa.pub`. You must keep your private key safe. The public key
can be safely shown to others.

### Transfer your public key to server

To enable host public key login, you must put your public key under the
`$HOME/.ssh` directory in the remote server. There are two ways.

+ Copy public key manually: Copy the content of the public key. Log in to your
server and paste the public key to the file `$HOME/.ssh/authorized_keys` (if
this file does not exist, create the file first.).

+ Use ssh-copy-id: In some systems, there is tool called `ssh-copy-id` which
an copy the content of the public key to `$HOME/.ssh/authorized_keys` in the
host server. You can use the following command to copy the public key to the
server:

```bash
ssh-copy-id user@host_ip
```

###  login to the server

After setting up public key login, you can log in to the server without using
password:

```
ssh -p xxx user@host_ip
```

The server will verify if the public key under user's directory corresponds
your private key. If they match, you will log in to the server automatically.

# References

+ [Generate key fingerprint in different format.](https://stackoverflow.com/questions/9607295/calculate-rsa-key-fingerprint)
+ [What is ssh key fingerprint?](https://superuser.com/questions/421997/what-is-a-ssh-key-fingerprint-and-how-is-it-generated)
+ [Difference between authorized_keys and known_hosts.](https://security.stackexchange.com/a/20710/174338).
+ [What does it mean that a host's authenticity can not be established?](https://superuser.com/a/421084/736190)?
+ [How to use public key authentification for ssh.](https://www.linode.com/docs/security/authentication/use-public-key-authentication-with-ssh/).
+ [What is known_hosts file?](https://unix.stackexchange.com/a/88872/221410).
+ [Difference between DSA, ECDSA, RSA?](https://askubuntu.com/questions/363207/what-is-the-difference-between-the-rsa-dsa-and-ecdsa-keys-that-ssh-uses).

[^1]: The public keys of the host server are stored in `/etc/ssh` directory, for example: `ssh_host_dsa_key.pub`, `ssh_host_rsa_key.pub`, `ssh_host_ecdsa_key.pub`.
[^2]: To check the format of `known_hosts` file, see `man 7 sshd`.
[^3]: `SHA` has three algorithms in series, `SHA-1`, `SHA-2` and `SHA-3`. `SHA-1` is old, and the other two are newer.
