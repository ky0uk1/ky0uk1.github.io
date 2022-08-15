---
layout: post
title: Overthewire Bandit Level 0-5
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

## Level 0

This level is the intro to the game and teaches what you need to do to connect to the remote machine. We will use
a protocol called SSH to do our connection. Each level has reading material on the topics it is talking about so
if you don't know about SSH I recommend checking out, but basically SSH is a protocol for remote and secure connections to another machine.

The basic syntax is:

```sh
ssh {user}@{host} -p {port}
```

So for our purposes of connecting to the host bandit.labs.overthewire.org at port 2220 with the user bandit0 the command will be:

```sh
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

If whoever you are using Windows you can install a program called putty, on this [link](https://www.putty.org/).

Then on the putty window you can put the same informations, like in this image:

![Putty config](../images/puttyconfig.png)

After that the description tells us that the password for bandit0 is bandit0, so login and you should get a shell on the remote machine.

## Level 0 -> 1

The description of this level says that the password is stored in a file called readme on the home directory of the user.

So, when you log in to bandit0 you should see a ~, that means we are on the home directory of the user bandit0, like this:

![Bandit0 Home](../images/bandit0_home.png)

Since we are already on the home folder of the user, all we need to do is run the command:

```sh
ls -l
```

So we can list all the files within the directory. The -l is to present the files in a list format, showing more details. 
From left to right we can see the file permissions, the first r and w means that the creator of the file can read and write, the second r means that bandit0 can read the file, then we have the user that created, which is bandit1 and the group that can read it, which in this case is bandit0.
Lastly we have the date it was last edited and the name of the file, which is "readme".

So, this is the file we want. To read a file you can use the command:

```sh
cat {file}
```

Using this command to read the readme file we get our password, *boJ9jbbUNNfktd78OOpsqOltutMc3MY1*.

## Level 1 -> 2

For this level we need to get the password stored in a file called "-" on the home directory.

We can use the ls -l command from the previous level to see that indeed there is a file called "-" on the home folder. 

Unfortunately, the cat command like we did last level won't work, because the - will be interpreted as a parameter for another argument of the command, like in the ls command we use the *-*l to specify listing.

So to circumvent that, we can use a relative path, which means we basically tell the cat command to access the folder and then the file.

The final command will be like:

```sh
cat ./-
```

This means that the cat command will access the current working directory (which is . in the linux filesystem) and then accessing the file called "-".

With that we have the password for bandit2, *CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9*.

## Level 2 -> 3

This level is pretty straightforward, it says that the password is stored in a file called "spaces in this filename" on the home directory.

The auto complete of the shell already answers this for us, if we type "cat spaces" and press TAB, it will autocomplete with the first file located on the folder were at, so the final command will be like:

```sh
cat spaces\ in\ this\ filename
```

So, doing this cat command we get the password, *UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK*.

## Level 3 -> 4

In this one the password is in a file that is stored in a folder called inhere. From the home directory we can do a "ls -l" to see that there is a directory (which is represented by the d on the far left). To access a directory we can use

```sh
cd {folder name}
```

to go to this folder. So our command will be

```sh
cd inhere
```

and doing another ls -l we can't find anything on this folder. That is because the file is hidden and the "ls" command won't normally show hidden files. For us to see the hidden file we need to pass another argument, the "-a".

Our final command will be like this:

```sh
ls -la
```

With this we can show files like a list (-l) and show hidden files (-a), and indeed there is a file called ".hidden". Doing the cat command as always will print to the screen the password to the next level, *pIwrPrtPN36QITSp3EQaw936yaFoFgAB*.

## Level 4 -> 5

This level we need to find what file contains the password to the next level in the folder "inhere".

Since there is only 10 files, you could just do a "cat ./{filename}" one by one until you find the answer, but that is boring. Imagine if it had 100 files, that approach would take a lot of time and effort to pull of.

So in the spirit of learning, we are going to do a simple bash script to help us!

I always recommend writing code in the temp folder, since we do not want to left random files for other people. Remember that you're not the only one playing these games!

So, I'll create a folder in the temp directory with:

```sh
mkdir {foldername}
```

And then make a file like that:

```sh
vim /tmp/{foldername}/{filename}
```

With that, we are presented with the VIM interface and to edit the file you can press "i".

So, briefly going over what is the objective: We need to read all the files in the folder "inhere", which is in the home folder of our current user (bandit4).

To achieve this the script could iterate through the files in the folder and print its contents to the terminal, so we need to do a for loop.

The final code will look like this:

```sh
#!/bin/bash

for file in /home/bandit4/inhere/*; do
    cat $file
    printf "\n"
done
```

Going over the code from line 1 we have:
1. The `#!` is called a shebang, it is just there to say that this file is a bash script;
2. A for loop iterating through all files (that is why it is using `*` after the folder);
3. the cat command to print the contents of the current file, which is stored in a variable (in bash, accessing variables have the prefix `$`);
4. The printf command is used just to print a new line to the terminal (`\n` is the equivalent of new line) just so we can see each content more clearly;
5. The `done` marks the end to the for loop;

After writing the code you can exit and save the file by doing `esc` to exit insert mode and pressing `:x`.

To run the script just write the absolute path to it, like this:

```sh
/tmp/{foldername}/{filename}
```

Running the script we can see a line that only contains printable characters, that is the one we want, which is *koReBOKuIDDepwhWk7jZC0RTdopnAYKh*.

## Epilogue

With that, I declare the first writeup on the bandit series finished!

I'll try to write 5 levels in each post, but that probably won't happen because as we progress, each level will have a lot more explanation as it increases the difficulty. On the other hand, I'll stop explaining more basic stuff and focus more on new information.

Keep learning!