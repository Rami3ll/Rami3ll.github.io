---
layout: post
title: NahamCon 2022 CTF - challenge walkthrough.
Categories: CTF
---


## PREFACE
  Considering how big of a fan I am of Naham and John Hammond, this ctf was quit a big deal to me! And it was awesomeeeeeee, so much fun and involved categories which I really liked like malware analysis, I would only be posting solutions to the questions a humble noob like me could solve, so my apologies if you sought answers which are not present here <3
 nb: This will frequently be updated for a couple days.
 
## WARMUP CHALLENGES
  This category was created basically for beginners to get their feet wet... I guess cause they were easy on a whole other level, so hey lets get to it!
  
#### Flagcat
  This one's basic, I grabbed the flagcat file, read with cat, and that's pretty much it!
  ```
  ┌──(azazel㉿azazel)-[~/Documents/Nahamcon/General]
└─$ cat flagcat 
 ---------------------------------------- 
| flag{ab3cbaf45def9056dbfad706d597fb53} |
 ----------------------------------------
        ||
 (\__/) ||
 (•ㅅ•) //
 / 　 づ

  ```
  
#### Quirky
  On downloading the provided file, and reading it, it contained "Long ASCII texts with no line termnators" 
  ```
  ──(azazel㉿azazel)-[~/Documents/Nahamcon/General]
└─$ file quirky                                     
quirky: ASCII text, with very long lines (1612), with no line terminators
                                                                                                                                                                                                                                              
┌──(azazel㉿azazel)-[~/Documents/Nahamcon/General]
└─$ cat quirky                                      
\x89\x50\x4e\x47\x0d\x0a\x1a\x0a\x00\x00\x00\x0d\x49\x48\x44\x52\x00\x00\x00\x6f\x00\x00\x00\x6f\x01\x03\x00\x00\x00\xd8\x0b\x0c\x23\x00\x00\x00\x06\x50\x4c\x54\x45\x00\x00\x00\xff\xff\xff\xa5\xd9\x9f\xdd\x00\x00\x00\x02\x74\x52\x4e\x53\xff\xff\xc8\xb5\xdf\xc7\x00\x00\x00\x09\x70\x48\x59\x73\x00\x00\x0b\x12\x00\x00\x0b\x12\x01\xd2\xdd\x7e\xfc\x00\x00\x01\x25\x49\x44\x41\x54\x38\x8d\xd5\xd4\x31\x8e\xc3\x20\x10\x05\xd0\xb1\x5c\xd0\x25\x17\x40\x9a\x6b\xd0\x71\x25\xfb\x02\xb6\xf7\x02\xce\x95\xe8\xb8\x06\x92\x2f\x40\x3a\x0a\x94\xd9\x8f\x23\x45\xbb\xc5\x66\x68\x52\x2c\xa2\xe0\x21\x21\xcf\x0c\x83\x49\x7e\x0d\xfa\x1f\xcc\x44\x8b\xaf\x6b\xb0\x44\xac\xf2\x2e\x75\x72\xe3\x66\xea\x2a\x1d\x0c\x76\xc1\xe7\x82\x9d\x4c\x17\x27\x97\xc8\xd4\x4e\xae\x91\xd6\x62\xbb\x28\x75\x8e\xf5\x1a\xed\x2b\xc8\x37\x44\xbe\x73\xb4\x98\xaf\xf4\xdf\xf0\x1c\xf6\x5a\x7e\x16\xf6\x4f\x66\xb2\x64\x78\xf3\xc7\xee\x3a\xe8\x0f\xac\x25\x10\x39\x56\x79\x2f\x74\x71\xe3\x57\x94\x87\x11\x9d\xf1\xd8\x5b\x6c\x34\x79\x9d\x0f\x8f\xb3\xb2\xfb\x73\x53\xa5\x3b\x36\x33\xa2\xf8\x73\x60\x95\x52\xea\x10\xd3\xc5\xf0\x7e\x46\xf5\x9e\x77\x19\x6f\x6d\x4a\x76\x3a\x25\xa0\x49\xda\x05\xdd\x22\xab\x44\xbe\x38\x28\x25\xcd\xa5\x83\x92\x86\x82\x90\x0e\x14\x53\x67\x44\x1f\xd6\x39\xa0\xfe\xac\xb3\x9d\x95\xdd\x10\x19\x51\x89\x91\x3d\x21\xa4\xec\x58\x25\x3a\x76\xf2\x69\x68\xaf\x4c\x54\xe2\x2d\x2c\x1e\x95\xe4\xec\x59\x27\xae\x52\xd0\xb4\x34\x3c\xf3\x55\x89\x85\xf0\xc3\x71\x17\x0b\xdf\x42\x22\x27\x3a\xf1\x7e\xd1\x2d\x68\xaa\xa2\xb3\xe5\xdb\x3a\x56\xb2\xd1\xf9\xb9\x5f\xee\xa7\xf8\x0d\x69\xf5\x37\x77\x6e\xf8\x09\x97\x00\x00\x00\x00\x49\x45\x4e\x44\xae\x42\x60\x82  
  ```
 it is definitely looks like a dump of a file but what type?
 Lets just pass the file to cyberchef and see what he can cook up lol
  ![image](/assets/img/posts/Nahamcon/quirky1.png)
  
a QR code, awesome! cyberchef has a qr decoder yunno, so, on decoding, we got our flag!
  ![image](/assets/img/postsNahamcon/quirky2.png)
  ![image](/assets/img/postsNahamcon/quirky3)
   
#### Prisoner
  This was awesome, had a little hint from a comrade on this one, this was my second rendezvous with a python jail(check out [tryhackme's Tokyo Ghoul](https://www.tryhackme.com/room/tokyoghoul666) for a more challenging prison).
  From the challenge name I already knew that was what I would be dealing with, to get to it, deploy the instance, and ssh into it, automatically it sends us to jail, touche, to exit the script's hold ctrl+d and you should have the classic python ```>>> ``` IDLE prompt now, meaning we are still in some sorta python pty, this gives us the advantage of executing commands of either reading files directly or completely spawn a shell;) like the one we use to import a pty for tty stabilisation, here's my two methods:
  
  METHOD 1: spawning a pty shell to have full access to the environment.
  ![image](/assets/img/posts/Nahamcon/prisoner1.png)
  
  METHOD 2 : using the import library and executing arbitrary commands through the IDLE shell with the os.system()function
  all we just have to do here import the library and pass the commands we want to execute to the os.system library as a parameter.
  ![image](/assets/img/posts/Nahamcon/prisoner2.png)
  
 #### Exit vim
  Vim is said to be one of the first intimidating tool of every noob lmao This challenge was all sorts of funny, John hammond has an incredible sense of humor. once upon a time, vim used to be my worst nightmare, luckily for me I did the [Tryhackme-Vim:Toolbox](https://www.tryhackme.com/room/toolboxvim) room a couple weeks ago, now I barely even use nano lol. I'd advise you try out the room if you're "VImoPObIc" you could also checkout this [post](https://github.com/tayfun27/toolboxvim-writeup/blob/master/toolboxvim-writeup.md)!
there are esentially 3 ways to exit vim: you could either:: 
- :wq -- to write and save  (include the ":")
- :q! -- force quit     (include the ":")
- ZZ (capital " ee zee") this saves and quits. (My personal favourite)
 
 #### Crash Override
  This was personally my favourite task, if how I solved it was the intended way? I have no idea but keep reading you might learn a new netcat trick! hehee:)
  Here we are provided with three essential files: 
  - A crash override file (ELF 64-bit LSB pie executable)
  - A makefile (An essential file!)
  - Crash_override.c (The C source code)
    lately I have been having frequent encounters with C I just had to learn some basics, one of those "basics" are a Makefile
   
   ```A makefile is a special file (named as 'Makefile' only) that consists of targets, dependencies and commands, structured in a way that makes it easy for a programmer to compile the program.```
  
  Imagine being a C/C++ programmer and having to type "gcc -fnostack-protector blah blah blah" every single time you write a program and want to compile into an executable or object file for every test! stressful asf yeah? after all tech exists to make life easy, hence the relevance of a makefile!
  Luckily for us we were provided a make file, yay! so we know how to compile the C program that we were given, to get to it::
  check [this page](https://linoxide.com/learn-linux-makefiles/) for more insight on makefiles
  The makefile instructions says:
  ```
┌──(azazel㉿azazel)-[~/Documents/Nahamcon/crashoverride]
└─$ cat Makefile                                                                                                  1 ⨯
all: 
	gcc -fno-stack-protector -z execstack crash_override.c -o crash_override
  ```
  Awesome we know how to compile, so just pass the switches to gcc and the C soure code.
  Its always helpful to read the source code yunno::
  ![image](/assets/img/posts/Nahamcon/crashoverride1.png)
  
It looks like this source file is the other half of what we have on our netcat connection, from my noob understanding of the script, once compiled, if executed on a computer, it reads a file called "flag.txt" by passing it to the fopen function, passes through a couple if statements and exits its runtime.
On execution;
```
┌──(azazel㉿azazel)-[~/Documents/Nahamcon/crashoverride]
└─$ ./crash_override 
HACK THE PLANET!!!!!!
flag?
```
How to execute it on the remote computer? We already have access through netcat, so to pass the compiled binary I'm just going to redirect the output of the ELF binary into our netcat session!
  ![image](/assets/img/posts/Nahamcon/crashoverride2.png)  
  
And Boom! we have our flag.  
 
#### Read the rules
  This one is a walk in the park, just go to the ctf help page and read the source code!
  for compensation of my laziness to include a screenshot, the flag is located in a comment on line 273:)

#### Wizard
  This one was fun, it was centered around encoding and decoding, all you have to do is cook your way to the flag ;) 
Heres the fix, we answer 6 questions correctly from Gandalf the great and he gives us our flag, cool!
first was a binary to ASCII question
![image](/assets/img/posts/Nahamcon/cook1.png)

Next question was a hex to ASCII conversion::
![image](/assets/img/posts/Nahamcon/cook2.png)

Next question was an Octal to ASCII question, this wizard was starting to really push it
we would have to conert from octal -- base10 aka int aka Decimal --- hex--- ascii just like regular Maths yunno!
I used [Rapid Tables](https://www.rapidtables.com/convert/number/hex-to-ascii.html) for this one, since it has the capabilities to do a direct octal to hex conversion:
![image](/assets/img/posts/Nahamcon/cook3.1.png)
  ![image](/assets/img/posts/Nahamcon/cook3.2.png)

Next question, is a decimal to ASCII conversion
![image](/assets/img/posts/Nahamcon/cook4.1.png)
  ![image](/assets/img/posts/Nahamcon/cook4.2.png)

FIfth question is a base64 conversion to ascii
```
┌──(azazel㉿azazel)-[~/Documents/Nahamcon]
└─$ echo -n 'QmFzZXMgb24gYmFzZXMgb24gYmFzZXMgb24gYmFzZXMgOik=' | base64 -d
Bases on bases on bases on bases :)                                                                                                                      

```
For the 6th question, a Little to Big-Endian conversion
to be honest this was the first time I had to deal with endian conversions, but here's what I did after a liitle reseach:
I used this [site](https://blockchain-academy.hs-mittweida.de/litte-big-endian-converter/) dont forget to change to hex if you use this site to solve.

Note that your result from that site converts little HEX endian to Big HEX endian and you would require a conversion from hex to ascii since the wizard demands a plaintext answer!
![image](/assets/img/posts/Nahamcon/cook6.png)
  ![image](/assets/img/posts/Nahamcon/cook6.2.png)
    ![image](/assets/img/posts/Nahamcon/wizard.png)
  And that's our flag, thank you Gandalf. lol
  
  ### Techncal Support
   For the last and easiest warmup challenge, all we have to do is join the discord server go to the "ctf-help" channel, and sensei John Hammond has a flag waiting there for us!
   
   
  Thank you for your time, this was a long read lol, this would be updated more frequently to include writeups for the remaining challenges!
  
  Salaam! [Rami3l](https://www.linkedin.com/in/daniel-johnson-9a39aa215/).
  
  
