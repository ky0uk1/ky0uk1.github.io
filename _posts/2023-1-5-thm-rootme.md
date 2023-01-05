---
layout: post
title: TryHackMe RootMe Box
---

Today we're going to take a look on a machine in TryHackMe called RootMe. It is supposed to be a simple ctf to get access to a machine and do a privilege escalation. So let's get started!

## Deploy the machine

This step is pretty simple, if you never used TryHackMe you can go to the [OpenVPN room](https://tryhackme.com/room/openvpn) to get started.

Connecting to the VPN and starting the machine, first thing to do is check if it is up by doing this command in the terminal:

```sh
ping {MACHINE_IP}
```

If you get a response, then you're good to go, the remote machine is up and running.

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

The real gem here is the /panel page, which has a upload section! Probably uploading something there will make it appear on the /uploads.

This could potentially lead to uploading anything we want into the remote server. Do you see the opportunity here?

## Getting a shell

If we take a closer look at cookies we can see that we have a PHPSESSID, which means the server is running on PHP. With that in mind I wonder if we can upload a malicious php file and run it on the /uploads to get a reverse shell?

Let's try that!

There's a nice and easy to use php reverse shell on github by [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell). 

After downloading it we need to change some things. Since we want a reverse shell, we need to pass the IP that is going to receive the shell, and that will be our IP. by deafult it is '127.0.0.1', so you can change to your IP on the tryhackme vpn. The port you can leave as whatever you want, I'm going to leave at 1337.

If you want to know how the script works I really recommend you read the code and the comments on the file, they are really helpful in understanding what is going on!

After the configs, we can close the editor and try to upload the file!

And it doesn't work... It seems that the upload form checks for files with php extension and blocks it. But a .php file is not the only type of extension PHP accepts, wwe can try others like .phtml, .php3, .php4 and so on.

Changing the extension to .phtml seems to work! the form accepted it, so now we access the /uploads and try to open the file we just uploaded.

But before that, we need a way to listen to that connection, and for that we'll use netcat. Netcat can be used to listen on the port you specify for any type of connection.

To use it I'll type: 
```sh
netcat -lvnp {PORT}
```

And with netcat listening on the port you specified, you can finally run the file that was uploaded to the /uploads.

And with that you get a shell!

using whoami we can see that we are www-data. We don't have any privileges, but we can try and find the user.txt file anyway. Running the find command like that:
```sh
find -name user.txt 2>/dev/null
```

This command basically searches for the file on the directory you're currently at and any subdirectories under the one you're at.
The -name defines that you want to search for a file with the name you put on after.
The 2>/dev/null part is just for that any attempt that leads to a bad behaviour (permission denied for example) is just redirected to null so it won't pop up on our output.

With that we can see that this file is in /var/www/user.txt.

## Privilege escalation

Now that we have a RCE to the target machine, we need to get root. To do that we will search for any SUID binaries. Basically an SUID means that the program can be executed as root, and with that we can use this to run commands as root.

To search for SUID files we will use the same command as before, like that:
```sh
find -perm -u=s -type f 2>/dev/null
```

We can see that a lot of files appeared, most of them are pretty common SUID binaries, but one that is odd and stuck out to me was python. With python as SUID we can run a python code that can give us a shell, and since it will run it as root, we will essentially get a root shell.

Using [GTFOBins](https://gtfobins.github.io) and searching for python SUID, we can find a page showing the command to run on a shell to get root.

Using that command and running whoami, we can see that we are, indeed, root!

Now usually root flag is on the /root folder. Going into this folder we actually find root.txt!

We succesfully pwned the machine and got root! I recommend getting a deeper look into how SUID, reverse shells and PHP works, since it is very common that you will have to use this knowledge to root machines.

Happy hacking!