---
layout: post
title: Overthewire Bandit Level 0
---

To start off my writeups I decided to do the first game I ever did, which was the Overthewire's Bandit.
This game is a more beginner friendly intro to what labs/games are and what you can expect.

Basically for a person who never did a wargame on Overthewire, the objective is completing levels.
Each level is a user in the machine we are connecting to, and completing the challenge on that user grants
the password for the next user and so on.

Remember that if you want to really learn what these games want to teach you, the best thing to do is do them
blindly (without reading a writeup like this one) and research on the internet the topics presented, not the
solutions. With that in mind, writeups (for me atleast) are just a way of seeing other ways to accomplish the same
solution, which you might have not thought of.

With all that out of the way, let's start.

### Level 0

This level is the intro to the game and teaches what you need to do to connect to the remote machine. We will use
a protocol called SSH to do our connection. Each level has reading material on the topics it is talking about so
if you don't know about SSH I recommend checking out, but basically SSH is a protocol for remote and secure connections to another machine.

The basic syntax is:

```sh
ssh {user}@{host} {port}
```

So for our purposes of connecting to the host bandit.labs.overthewire.org at port 2220 with the user bandit0 the command will be:

```sh
ssh bandit0@bandit.labs.overthewire.org 2220
```

If whoever you are using Windows you can install a program called putty, on this [link](https://www.putty.org/).

Then on the putty window you can put the same informations, like in this image:

![Putty config](../images/puttyconfig.png)

After that the description tells us that the password for bandit0 is bandit0, so login and you should get a shell on the remote machine.