---
layout: post
title: Deathnote Writeup
categories: [Vulnhub]
tags: [wordpress,webshell,sensitive information disclosure]
---

### Preface
  Hi! this is a walkthrough of the Vulnhub machine [Deathnote](https://www.vulnhub.com/entry/deathnote-1,739/),One of my favourite anime's of all time and described by the author as a Straight forward box, ranked 
Level - easy with a very helpful advice "don't waste too much time thinking outside the box". Initially I felt it was too easy to make a writeup about this machine but I encountered some things for the first time while hacking wordpress, and I hope it'll be of help to you just incase you get stuck;)
There are 2 intended ways of gaining Access, I'll walk you through both ways! The goal is simple...Get Root!

### Live Host Discovery
  Once deployed on a hypervisor, we need to find a way to find our targets ip, this we could do through an arp-scan with netdiscover or the arp-scan command utility::
- with arp-scan

```
┌──(azazel㉿azazel)-[~/Vulnhub/Deathnote]
└─$ sudo arp-scan 192.168.0.124/24
Interface: wlan0, type: EN10MB, MAC: e4:b3:18:45:da:c6, IPv4: 192.168.0.124
WARNING: host part of 192.168.0.124/24 is non-zero
Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.0.1	c8:ea:f8:dd:9b:fb	zte corporation
192.168.0.192	08:00:27:a5:27:3e	PCS Systemtechnik GmbH

2 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.9.7: 256 hosts scanned in 2.015 seconds (127.05 hosts/sec). 2 responded
```
- Or with netdiscover

```
sudo netdiscover -i wlan0 -r 192.x.x.x/24
```
> The above methods function through the same procedure of flooding the subnet with arp-requests, and any hosts active would reply with an arp-reply.


### Reconaissance: Port Scan
  To enumerate for services on our target::
```
┌──(azazel㉿azazel)-[~/Vulnhub/Deathnote]
└─$ nmap -sCV 192.168.0.192 -p- --min-rate 2000 -vv --open                                                                                                                                                                              130 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-02 18:21 WAT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
Initiating Ping Scan at 18:21
Scanning 192.168.0.192 [2 ports]
Completed Ping Scan at 18:21, 0.00s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:21
Completed Parallel DNS resolution of 1 host. at 18:21, 0.01s elapsed
Initiating Connect Scan at 18:21
Scanning deathnote (192.168.0.192) [65535 ports]
Discovered open port 22/tcp on 192.168.0.192
Discovered open port 80/tcp on 192.168.0.192
Completed Connect Scan at 18:21, 1.33s elapsed (65535 total ports)
Initiating Service scan at 18:21
Scanning 2 services on deathnote (192.168.0.192)
Completed Service scan at 18:21, 6.02s elapsed (2 services on 1 host)
NSE: Script scanning 192.168.0.192.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.25s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
Nmap scan report for deathnote (192.168.0.192)
Host is up, received syn-ack (0.00018s latency).
Scanned at 2022-05-02 18:21:15 WAT for 8s
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 5e:b8:ff:2d:ac:c7:e9:3c:99:2f:3b:fc:da:5c:a3:53 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCm89TpyON8aYXST7z9QwF/lZeqXiejtLOaVDmyxME7S2M1Z+57KxnB8ixV0MArX+cNTWO92xYlTiYKIKusPwrB43AArYrnHTLUg79N59GhbreA8yX2xxwuJq/+tu2ieMS7QORyKJVZCRwV/EVHknQyg+jrXLtD74C869l9wb3S9yIeSK1UGNrbdAgOPZtG4pFFRcKghC4wtuukqaE7uIv4yO8mHOo+p+V8+Ab3tbmIG2KZLbb3hW3vjKc4XxBnoJTWwsX6zkV/1HnfE+ptFzs0sHWpyiAKtUGs4AkfymcG5SDyuAkBBiABpBbzBK/Lzcjhb7dJGpqPac+M+lxIb+T1
|   256 a8:f3:81:9d:0a:dc:16:9a:49:ee:bc:24:e4:65:5c:a6 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCh/urbdjg8yYyJTf6zXc0OVNr9nZ8BXv26XthpOdSCdnIMLKNmlAbONw9KKJ05/BS/7T+zg/LHdA5FAAHL8OFw=
|   256 4f:20:c3:2d:19:75:5b:e8:1f:32:01:75:c2:70:9a:7e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAII9njk3MGTFBM1ueTjfLjhVUE0a485AyO7kFm0enSRap
80/tcp open  http    syn-ack Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.38 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:21
Completed NSE at 18:21, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.11 seconds
```
On visiting port 80, the ip automatically resolves to a "www.deathnote.vuln/wordpress/" and the page doesn't load, 

> In simple terms, this is as a result of a resolution conflict by DNS not recognising that site as the bearer of the ip.

![image](/assets/img/posts/vulnhub/Deathnote/site.png)

Easy fix! we would need to override this DNS resolution by including the ip and host name in our /etc/hosts file like this:
```
sudo echo "$ip deathnote.vuln" >> /etc/hosts
```
Or vim or Nano into the file with sudo and edit the file:

![image](/assets/img/posts/vulnhub/Deathnote/2hosts.png)

Awesome, "It works!" ;) another really funny thing is the /wordpress/ directory gave it away as a wordpress site without a directory enumeration, we are in for a world of fun man!

![image](/assets/img/posts/vulnhub/Deathnote/3site.png)
 
Lying carelessly in the comments section is kira's creds, Light should know better yunno

![image](/assets/img/posts/vulnhub/Deathnote/4creds.png)

awesome, we got creds, I just let dirsearch loose in the background so we don't miss anything and to find an admin login page! 
By default it's always ``` /wp-admin/ ``` tried out the creds we have for a user "kira" on ssh, didn't work.

![image](/assets/img/posts/vulnhub/Deathnote/dirsearch.png)

But on the admin page it does:

![image](/assets/img/posts/vulnhub/Deathnote/5login.png)


Awesome, we have full access to admin dashboard, definitely we would have access to editor tools, to spawn a reverse shell could happen in so many ways, we could either:
- upload a vulnerable plugin or theme and exploit it 
- use the editor feature to edit on of the many php files within either the editor or plugin section
- Or upload a shell as a plugin in zip format.

I prefer the second, no stress, just edit any php file by replacing with reverse shellcode from and navigate to it to generate a reverse shell callback to our attack machine
lets get to it! you could grab a php shell code [here](https://github.com/pentestmonkey/php-reverse-shell) or your go-to reverse shellcode and edit a php file like so:

![image](/assets/img/posts/vulnhub/Deathnote/theme-edit.png)

on clicking update file, I come across a weird error, which is what spiced up this box a little:
> Unable to communicate back with site to check for fatal errors, so the PHP change was reverted. You will need to upload your PHP file change by some other means, such as by using SFTP.

I guess this was as a result of the site being able to detect a "poorly coded" theme or plugin, at least from the viewpoint of WordPress Core.
read more about this error [here](https://stackoverflow.com/questions/52671255/wordpress-editor-not-updating-files-unable-to-communicate-back-with-site-to-che)


### Exploitation
After much playing around and a bunch of fails with plugins(this deactivate, edit and activate doesn't work on plugins or at least it didn't for me), I was able to bypass this by deactivating the theme whose php file I wish to replace with shellcode, I used the Twenty Twenty::

![image](/assets/img/posts/vulnhub/Deathnote/theme-deactivate.png)

And then editing and updating the php file into our shell code(whilst inactive) and then reactivate it back::

![image](/assets/img/posts/vulnhub/Deathnote/shell.png)
![image](/assets/img/posts/vulnhub/Deathnote/edit-success.png)

It allows us successfully edit a theme while it's deactivated!

![image](/assets/img/posts/vulnhub/Deathnote/theme-activate.png)

And finally, after setting up a listener to activate the shell and generate our callback, we could navigate to a page which doesn't exist OR navigate to the 404.php page; I prefer terminal::

![image](/assets/img/posts/vulnhub/Deathnote/shell-activate.png)


### Initial Access
  Hell yeah, we got a shell! But not a stable one(check out my [tty cheatsheet here]() if you have issues with shell stabilization) if you're not so sure you could use the ```tty``` cmd let's get a stable tty::
![image](/assets/img/posts/vulnhub/Deathnote/tty.png)

In Kira's home dir, we have a kira.txt file but is unreadable *sigh*
One amazing thing about wordpress sites is that it saves MySQL backups for the base configuration files of the wordpress site in the same dirctory as the resources where the site runs on!
(``` /var/www/html/wordpress```)called wp-config.php always search for this first, it usually contains a user creds.

![image](/assets/img/posts/vulnhub/Deathnote/creds.png)

we get L's creds from here! now we can login over ssh.
  ![image](/assets/img/posts/vulnhub/Deathnote/ssh.png)
  
On login we meet a note L left us in brainfuck, decode that and we have::
```
i think u got the shell , but you wont be able to kill me -kira
```
  
### www-data turnt r00t!
  Welp Kira has no sudo permissions, neither does he have any SUID binaries and even after scp..ing a privesc enumeration script I had nothing other than kernel exploits, after much playing around, I found a "misa" file in the /var directory
 the misa file just says:
 ```
l@deathnote:~$ cd /var
l@deathnote:/var$ ls
backups  cache  lib  local  lock  log  mail  misa  opt  run  spool  tmp  www
l@deathnote:/var$ cat misa
it is toooo late for misa 
l@deathnote:/var$ 
 ```
And an L folder in the /opt directory with a wav file telling us to use cyber chef, say no more:
![image](/assets/img/posts/vulnhub/Deathnote/hint.png)  
 
The highest numeric is 9 and the highest alphabet is f beyond reasonable doubt we have a Hex sequence here! decode that::
 ![image](/assets/img/posts/vulnhub/Deathnote/wav.png)
 
We get a base64 text, convert that::
  ![image](/assets/img/posts/vulnhub/Deathnote/kira.png)
  
 and we have Kira's creds!
 
Now we just login over ssh as kira:
![image](/assets/img/posts/vulnhub/Deathnote/kira-login.png)

We finally have sufficient privileges to read our kira.txt file, which just has a base64 encoded text again::
```
┌──(azazel㉿azazel)-[~/Utils]
└─$ echo -n cGxlYXNlIHByb3RlY3Qgb25lIG9mIHRoZSBmb2xsb3dpbmcgCjEuIEwgKC9vcHQpCjIuIE1pc2EgKC92YXIp | base64 -d
please protect one of the following 
1. L (/opt)
2. Misa (/var)             
```

We already know whats in the misa file and the L folder, next up, let's get root!
checking sudo permissions, Kira can run everything with sudo!

![image](/assets/img/posts/vulnhub/Deathnote/root.png)
Rooooooooooooooted.


## Second Intended
  Man I found a clue that completely flew over my head until I gained foothold, maybe cause I was itchy to use a webshell haha, but here goes nothing::
 checking the robots.txt...```$ip/robots.txt```
 we have a clue:
 
 ```
fuck it my dad 
added hint on /important.jpg

ryuk please delete it
```
From directory enum, we had an uploads folder,``` $ip/wordpress/wp-content/uploads/2021/07``` we could check for our "important.jpg" there were so many images and none such a name, I just ignored those 
and went after the two suspicious text files::

![image](/assets/img/posts/vulnhub/Deathnote/uploads.png)

The notes.txt file contain possible passwords and the user.txt contains possible users, we could grab those and bruteforce ssh with hydra!

![image](/assets/img/posts/vulnhub/Deathnote/hydra.png)

Boom we got L's creds! now just login as L go to /opt get Kira's creds, login as kira and become root!

Greetings, from Rami3l.

