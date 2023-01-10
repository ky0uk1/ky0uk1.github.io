---
layout: post
title: TryHackMe Bounty Hacker
---

This writeup is on the Bounty Hacker room on Tryhackme. It is a simple ctf to get root on the system, so let's see how we can do this!

## Reconnaissance

Getting started after connecting to the VPN is to check for open ports on the target machine. I'm using nmap for this:

```sh
nmap -sC -sV $IP
```

Running nmap gives us ftp and ssh. The most important one right now is ftp. It is running vsftpd version 3.0.3, but the important thing is that the ftp allows anonymous connections, so we can login without a password as anonymous.

Now, knowing that, we can use the ftp command to login as anonymous, like that:

```sh
ftp `$IP`
```

Putting login as anonymous we can see that we are indeed logged. Doing an `ls` on the terminal gives us 2 files, `locks.txt` and `task.txt`

We can transfer both files to our machine to get a closer look, and after that log out of the ftp.

Doing a `cat` on the locks.txt we can see that it is a list of tasks, signed by someone named `lin`. Seeing the contents of the other file seems to show a list of names, or rather passwords. We can use this list to bruteforce passwords on ssh for some user named lin!

So, let's try that!

## Getting a shell

Now that we have a potential username and a list of passwords, we can bruteforce on the ssh to get a shell. For bruteforcing the password I'll use hydra, like this:

```sh
hydra -l lin -P locks.txt ssh://$IP
```

Within seconds we get a match, it seems that there is indeed a login name of lin and the password is RedDr4gonSynd1cat3. Using ssh to check the login and password we can see that we are in!

Doing a `ls` on the user's Desktop folder gives us the user.txt file with a flag.

Now, time for getting root!

## Privilege Escalation

It seems that this user doesn't have root privileges, so we need to find a way to get it.