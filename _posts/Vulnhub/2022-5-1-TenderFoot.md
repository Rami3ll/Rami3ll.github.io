---
layout: post
title: TenderFoot:1 Writeup
categories: [Vulnhub]
tags: [Enumeration]
---
 
## Preface
  Hey there! This is my writeup for the Vulnhub machine [TenderFoot](https://www.vulnhub.com/entry/tenderfoot-1,581/), Its described by it's author as "A very Easy Box for beginners", and is recommended for folks new to Vulnhub like myself. 
The task is simple, grab all 3 flags (user1.txt, user2.txt, proof.txt).
 
### Live Host Discovery
  This could essentially be done in 3 ways(that I know of):
 - Arp-scan
 - Netdiscover
 - Nmap ping sweep scan aka ICMP packet reception scan
My Go-to is Netdiscover:

```
sudo netdiscover -i wlan0 -r 192.168.x.x/24
```
- sudo because netdicover requires root permission to inject & sniff packets across a local network
- The netdiscover command utility
- -i to scan a selected interface
- -r to scan a specified range of subnet  (Defaults take forever!)

### Port Scan
  To discover an attack surface:
```
┌──(azazel㉿azazel)-[~/Vulnhub/TenderFoot]
└─$ sudo nmap -sV -sC -sS  192.168.0.141 -Pn -p- --min-rate 2000 -vv                                            
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-02 04:17 WAT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 04:17
Completed NSE at 04:17, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 04:17
Completed NSE at 04:17, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 04:17
Completed NSE at 04:17, 0.00s elapsed
Initiating ARP Ping Scan at 04:17
Scanning 192.168.43.33 [1 port]
Completed ARP Ping Scan at 04:17, 0.08s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 04:17
Completed Parallel DNS resolution of 1 host. at 04:18, 13.00s elapsed
Initiating SYN Stealth Scan at 04:18
Scanning 192.168.43.33 [65535 ports]
Discovered open port 22/tcp on 192.168.43.33
Discovered open port 80/tcp on 192.168.43.33
Completed SYN Stealth Scan at 04:18, 1.36s elapsed (65535 total ports)
Initiating Service scan at 04:18
Scanning 2 services on 192.168.43.33
Completed Service scan at 04:18, 6.02s elapsed (2 services on 1 host)
NSE: Script scanning 192.168.43.33.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.11s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.01s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.00s elapsed
Nmap scan report for 192.168.43.33
Host is up, received arp-response (0.00034s latency).
Scanned at 2022-05-02 04:18:11 WAT for 8s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a2:b7:2d:95:e1:06:7f:a3:f1:8e:bc:5b:4c:29:19:61 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBVksY9N/ofJe604i4zM4st08JHma+9EE1bag9Iq5aSms9Hte+K8TwHxuq/n62PXSHUV0gJi//1ijo+xBBOt1OLOtEnyGZjtNeBfUqiFjXqgB4dhZk1kMRHYvuHr2zIDlMjOGKol7XXWqNtUsGaGsXWwyh7vH37nhKthZQT8VHodrM9IniaAkKA2xneI9z5AGPQVc09edoAHDa8EnzJFZPwQCkyBP+WKEYS1kAoyTwoIy2JSbRZny7/vlV1oD+r1arS2oD+YJ6cVlFk+fM7Rln2eKp4vZmdcWJte508WERZ5t1rp7ntRO3GlQ5Ed0umpQODLEbK0ptiPkOtR6kh0v7
|   256 42:0c:c9:6d:1d:e9:84:19:6a:8a:d5:51:2c:69:c6:98 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBipQCFF4Aerg0pRwZ9y+le1WRmZlvYG+gfpOcrbDzOkTYzrXzWoyCdH9D7CX0LBioHqa5PB1xybTmVzwCgecgA=
|   256 14:4d:74:42:78:67:9b:f3:dd:00:40:24:4d:12:c9:de (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH1227M7ks61pC0yFPWcqzN/62PAaQa9zbEvLDWU7yLq
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 08:00:27:46:83:29 (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 04:18
Completed NSE at 04:18, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.15 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65542 (2.622MB)
```


We got http and ssh, cool
On http, its just default landing page we meet,
![image](/assets/img/posts/vulnhub/TenderFoot/landing.png)

checking the source code, at the bottom 
```
<!--
G00D! 
check /robots.txt directory.
-->

```
we got a hint to check the robots.txt::
```

Here is a directory open it 

/hint

```
Okay a clue to a hidden directory let's see where this leads, On first sight it seems blank but on viewing the source, hidden at the extreme bottom is an encoded text screaming base32(Can be identified with all upper casing characters and paddings(==)although paddings are not always present)::
```
<!--
EBPV6X27L5PV6X27L5PV6X27L5PV6X27L4FHYICOGB2GQ2LOM4QEQZLSMUQSAIBAEAQCA7AKPQQFI4TZEBZW63LFORUGS3THEBSWY43FEF6AUIBNFUWS2LJNFUWS2LJNFUWS2LJNFUWS2LIKIVXHK3LFOJQXIZJANVXXEZJAHIUQ====

Decrypt it!
-->
```
On decoding it says:
![image](/assets/img/posts/vulnhub/TenderFoot/deadend1.png)

welp that's sad, moving on, I decide to do a directory enumeration, after trying out around 5 wordlists 

I finally get a hit with seclists::
![image](/assets/img/posts/vulnhub/TenderFoot/dirsearch.png)

We know /hint is a deadend let's visit /fotocd


![image](/assets/img/posts/vulnhub/TenderFoot/brainfuck.png)

lesserthan, greaterthan, full stops, straight brackets, addition signs, and what not, no doubt its the esoteric brainfuck::
We could decode with either [this interperter](https://copy.sh/brainfuck/) or [decode.fr](https://www.dcode.fr/brainfuck-language)
![image](/assets/img/posts/vulnhub/TenderFoot/brainfuck-decode.png)

Awesome, we have a better clue, a base64 encoded header and a hint that we would need to look for a username:
On decoding the header we get::
```                                                                                                             
┌──(azazel㉿azazel)-[~/Vulnhub/TenderFoot]
└─$ echo -n JDk5OTkwJA== | base64 -d

$99990$  
```
We have what seems to be a password, to find complete creds of a user, we could either do a password spray with hydra to find a username OR enumerate once again.
Due to hydra time constraints and limited threads on ssh bruteforcing, I enumerated again, except this time with extensions(I did leave hydra to bruteforce in the background:)):
```
dirsearch -u http://192.168.0.141/ -w  /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -t 250 -e js,php,txt,xml 
```
we get the entry.js has a user, "monica"


![image](/assets/img/posts/vulnhub/TenderFoot/username.png)

Lets login on ssh with her creds! monica:$99990$

### Initial Access
  ![image](/assets/img/posts/vulnhub/TenderFoot/access1.png)

And we're in! Lets play around 😈 Here's our first flag! 
Next we have this Joey directory with a whole bunch of other subdirectories :) just tab autocomplete your way to the relevant file
  ![image](/assets/img/posts/vulnhub/TenderFoot/access2.png)
  
We have a new clue, the fotocd subdirectory::

![image](/assets/img/posts/vulnhub/TenderFoot/parent-dir.png)

We have access to the parent directory of the site and have a zip protected file and a note.txt file waiting for us, just grab those and check them out:
First the note.txt::

```
Here is a zip file for you, unzip it !

"#9175"

```
A password to our zip? this is almost too easy.
Unzipping our file, we have the Joey.zip file containing another zip protected file and a note.txt, *sigh

The note file says:
```
┌──(azazel㉿azazel)-[~/Vulnhub/TenderFoot]
└─$ cat note.txt
=======================================================
=======================================================
Not this time DUDE!

Just G00GLE and learn how to crack zip passwords :)

you will learn new thing!
=======================================================
=======================================================
```
Touche::
crack the gift.zip file and we got this:
![image](/assets/img/posts/vulnhub/TenderFoot/crack.png)

Just a clue for us to find SUID  binaries, of course we would have done that if he didn't lead us on😂
Checking for SUID binaries, I spotted an odd binary, on executing it, we became another user, Awesome
Cop the user2.txt flag.
![image](/assets/img/posts/vulnhub/TenderFoot/chandler.png)

Now to gain elevated privileges, 
> if there's anything this box taught me is always try to get user creds to login properly over a pty cause sometimes the SUID Biniaries set for that user might not really display as they should.

Luckily for us we have a new clue, a base32 encoded charset::


```
┌──(azazel㉿azazel)-[~/Vulnhub/TenderFoot]
└─$ echo -n OBQXG43XMQ5FSMDVINZDIY3LJUZQ==== | base32 -d

passwd:Y0uCr4ckM3        
```
Cool this might just be chandlers creds. lets try logging in

### Root tooth 
Even without [GTFOBins](https://gtfobins.github.io/gtfobins/ftp/#sudo) enabling sudo on ftp is a clean shot to get root.
![image](/assets/img/posts/vulnhub/TenderFoot/root.png)

And that's it, fully compromised!

Due to the emergence of findings of newer vulnerabilities and evolving tech, there are so many ways to get root instantly on this machine, one of which is Pwnkit(CVE-2021-4034)which allows us abuse pkexec by virtue of SUID bit set on the binary, nonetheless those are all unintended ways and not ideal.:)

> The difference between a hacker and a Noob, is that a hacker has failed more times than a Noob has ever tried.
 
Thanks for the read.

From Rami3l with <3.

Salaam.
