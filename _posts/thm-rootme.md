---
layout: post
title: Overthewire Bandit Level 0-5
---

Today we're going to take a look on a machine in TryHackMe called RootMe. It is supposed to be a simple ctf to get access to a machine and do a privilege escalation. So let's get started!

## Deploy the machine

This step is pretty simple, if you never used TryHackMe you can go to the [OpenVPN room](https://tryhackme.com/room/openvpn) to get started.

Connecting to the VPN and starting the machine, first thing to do is check if it is up by doing this command in the terminal:

```sh
ping {MACHINE_IP}
```

## Reconnaissance

First step in any box is to map out open ports and services of the targeted machine. To do that I'll use *nmap*.

```sh
nmap -sC -sV {MACHINE_IP}
```

And this is the result:

```sh
Nmap scan report for 10.10.169.51
Host is up (0.21s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: HackIT - Home
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.99 seconds
```

From the result we can see that the port 22 with OpenSSH 7.6p1, and 80 with Apache 2.4.29 is open.

Accessing the webpage on the port 80 gives us just a title and a paragraph saying "can you root me?". Yes we do!

Inspecting the page gives out nothing, there's no comments left behind or code.

When we have a simple webpage with not much, we can always try enumerating directories. For this I'll use GoBuster and the directory medium wordlist from [dirbuster](https://raw.githubusercontent.com/daviddias/node-dirbuster/master/lists/directory-list-2.3-medium.txt).

```sh
gobuster dir -u http://{MACHINE_IP}/ -w {PATH_TO_WORDLIST}
```

Doing 3% of the wordlist already gives some interesting folders to look at, like */uploads* and */panel*.