---
layout: post
title: Paper writeup
categories: [HackTheBox]
tags: [LFI, Wordpress, RCE, CVE, polkit]
---

## Premise 
	Hey, this is my writeup for the Hackthebox machine: paper, my first HTB non-starting point machine lmao.

![image](https://www.hackthebox.com/achievement/machine/880322/432)


### Enumeration
	As always, lets scout our attack surface with a quick port scan
```
┌──(azazel㉿azazel)-[~/Hackthebox/paper]
└─$ cat scans.txt                                                                                                 1 ⚙
# Nmap 7.92 scan initiated Wed Jun  1 17:47:31 2022 as: nmap -sV -sC -p- --min-rate 2000 -vv -Pn -oN scans.txt 10.129.208.103
Increasing send delay for 10.129.208.103 from 0 to 5 due to 86 out of 285 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 5 to 10 due to 157 out of 522 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 10 to 20 due to 142 out of 473 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 40 to 80 due to 157 out of 522 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 160 to 320 due to 114 out of 379 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 320 to 640 due to 152 out of 505 dropped probes since last increase.
Increasing send delay for 10.129.208.103 from 640 to 1000 due to 145 out of 483 dropped probes since last increase.
Warning: 10.129.208.103 giving up on port because retransmission cap hit (10).
Nmap scan report for 10.129.208.103
Host is up, received user-set (0.20s latency).
Scanned at 2022-06-01 17:47:31 WAT for 136s
Not shown: 64046 closed tcp ports (conn-refused), 1486 filtered tcp ports (no-response)
PORT    STATE SERVICE  REASON  VERSION
22/tcp  open  ssh      syn-ack OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:05:ea:50:56:a6:00:cb:1c:9c:93:df:5f:83:e0:64 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDcZzzauRoUMdyj6UcbrSejflBMRBeAdjYb2Fkpkn55uduA3qShJ5SP33uotPwllc3wESbYzlB9bGJVjeGA2l+G99r24cqvAsqBl0bLStal3RiXtjI/ws1E3bHW1+U35bzlInU7AVC9HUW6IbAq+VNlbXLrzBCbIO+l3281i3Q4Y2pzpHm5OlM2mZQ8EGMrWxD4dPFFK0D4jCAKUMMcoro3Z/U7Wpdy+xmDfui3iu9UqAxlu4XcdYJr7Iijfkl62jTNFiltbym1AxcIpgyS2QX1xjFlXId7UrJOJo3c7a0F+B3XaBK5iQjpUfPmh7RLlt6CZklzBZ8wsmHakWpysfXN
|   256 58:8c:82:1c:c6:63:2a:83:87:5c:2f:2b:4f:4d:c3:79 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBE/Xwcq0Gc4YEeRtN3QLduvk/5lezmamLm9PNgrhWDyNfPwAXpHiu7H9urKOhtw9SghxtMM2vMIQAUh/RFYgrxg=
|   256 31:78:af:d1:3b:c4:2e:9d:60:4e:eb:5d:03:ec:a0:22 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKdmmhk1vKOrAmcXMPh0XRA5zbzUHt1JBbbWwQpI4pEX
80/tcp  open  http     syn-ack Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
| http-methods: 
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
|_http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
|_http-title: HTTP Server Test Page powered by CentOS
443/tcp open  ssl/http syn-ack Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
|_ssl-date: TLS randomness does not represent time
| http-methods: 
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
|_http-title: 400 Bad Request
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US/emailAddress=root@localhost.localdomain
| Subject Alternative Name: DNS:localhost.localdomain
| Issuer: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US/emailAddress=root@localhost.localdomain/organizationalUnitName=ca-3899279223185377061
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-07-03T08:52:34
| Not valid after:  2022-07-08T10:32:34
| MD5:   579a 92bd 803c ac47 d49c 5add e44e 4f84
| SHA-1: 61a2 301f 9e5c 2603 a643 00b5 e5da 5fd5 c175 f3a9
| -----BEGIN CERTIFICATE-----
| MIIE4DCCAsigAwIBAgIIdryw6eirdUUwDQYJKoZIhvcNAQELBQAwgY8xCzAJBgNV
| BAYTAlVTMRQwEgYDVQQKDAtVbnNwZWNpZmllZDEfMB0GA1UECwwWY2EtMzg5OTI3
| OTIyMzE4NTM3NzA2MTEeMBwGA1UEAwwVbG9jYWxob3N0LmxvY2FsZG9tYWluMSkw
| JwYJKoZIhvcNAQkBFhpyb290QGxvY2FsaG9zdC5sb2NhbGRvbWFpbjAeFw0yMTA3
| MDMwODUyMzRaFw0yMjA3MDgxMDMyMzRaMG4xCzAJBgNVBAYTAlVTMRQwEgYDVQQK
| DAtVbnNwZWNpZmllZDEeMBwGA1UEAwwVbG9jYWxob3N0LmxvY2FsZG9tYWluMSkw
| JwYJKoZIhvcNAQkBFhpyb290QGxvY2FsaG9zdC5sb2NhbGRvbWFpbjCCASIwDQYJ
| KoZIhvcNAQEBBQADggEPADCCAQoCggEBAL1/3n1pZvFgeX1ja/w84jNxT2NcBkux
| s5DYnYKeClqncxe7m4mz+my4uP6J1kBP5MudLe6UE62KFX3pGc6HCp2G0CdA1gQm
| 4WYgF2E7aLNHZPrKQ+r1fqBBw6o3NkNxS4maXD7AvrCqkgpID/qSziMJdUzs9mS+
| NTzWq0IuSsTztLpxUEFv7T6XPGkS5/pE2hPWO0vz/Bd5BYL+3P08fPsC0/5YvgkV
| uvFbFrxmuOFOTEkrTy88b2fLkbt8/Zeh4LSdmQqriSpxDnag1i3N++1aDkIhAhbA
| LPK+rZq9PmUUFVY9MqizBEixxRvWhaU9gXMIy9ZnPJPpjDqyvju5e+kCAwEAAaNg
| MF4wDgYDVR0PAQH/BAQDAgWgMAkGA1UdEwQCMAAwIAYDVR0RBBkwF4IVbG9jYWxo
| b3N0LmxvY2FsZG9tYWluMB8GA1UdIwQYMBaAFBB8mEcpW4ZNBIaoM7mCF/Z+7ffA
| MA0GCSqGSIb3DQEBCwUAA4ICAQCw4uQfUe+FtsPdT0eXiLHg/5kXBGn8kfJZ45hP
| gcuwa5JfAQeA3JXx7piTSiMMk0GrWbqbrpX9ZIkwPnZrN+9PV9/SNCEJVTMy+LDQ
| QGsyqwkZpMK8QThzxRvXvnyf3XeEFDL6N4YeEzWz47VNlddeqOBHmrDI5SL+Eibh
| wxNj9UXwhEySUpgMAhU+QtXk40sjgv4Cs3kHvERvpwAfgRA7N38WY+njo/2VlGaT
| qP+UekP42JveOIWhf9p88MUmx2QqtOq/WF7vkBVbAsVs+GGp2SNhCubCCWZeP6qc
| HCX0/ipKZqY6zIvCcfr0wHBQDY9QwlbJcthg9Qox4EH1Sgj/qKPva6cehp/NzsbS
| JL9Ygb1h65Xpy/ZwhQTl+y2s+JxAoMy3k50n+9lzCFBiNzPLsV6vrTXCh7t9Cx07
| 9jYqMiQ35cEbQGIaKQqzguPXF5nMvWDBow3Oj7fYFlCdLTpaTjh8FJ37/PrhUWIl
| Li+WW8txrQKqm0/u1A41TI7fBxlUDhk6YFA+gIxX27ntQ0g+lLs8rwGlt/o+e3Xa
| OfcJ7Tl0ovWa+c9lWNju5mgdU+0v4P9bqv4XcIuyE0exv5MleA99uOYE1jlWuKf1
| m9v4myEY3dzgw3IBDmlYpGuDWQmMYx8RVytYN3Z3Z64WglMRjwEWNGy7NfKm7oJ4
| mh/ptg==
|_-----END CERTIFICATE-----
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jun  1 17:49:47 2022 -- 1 IP address (1 host up) scanned in 136.04 seconds
```

	On visiting port 80 we are greeted with the centOS Apache landing page and port 443 is simply hosting an https version of the site, source code inspections yield nothing, so I progress with a nikto scan for any abnormalities that could be leveraged for a foothold,

![image](/assets/img/posts/hackthebox/paper/nikto.png)

	From the web vuln scan I deduced that the backend server wants to be identified with the "office.paper" FQDN and this was smuggled as a shady header, which I confimed by sending a bad request with netcat to retrive header content:

![image](/assets/img/posts/hackthebox/paper/netcat.png)

so say less, we add the absolute domain name to our hosts file:
```
┌──(root💀azazel)-[/home/azazel/Hackthebox/paper]
└─# echo -n "10.129.208.103 office.paper" >> /etc/hosts

```
	Awesome, this time around we have a proper site, which I detected was crafted with wordpress from the bottom content hehee, this just got hella fun! 

![image](/assets/img/posts/hackthebox/site.png)

I also noticed comments talking about a group chat but couldn't make much of it(at that point). Anyways, since this is wordpress lets bust out wpscan

![image](/assets/img/posts/hackthebox/wpscan.png)

![image](/assets/img/posts/hackthebox/wpscan-enum.png)

Things to note:
~ from the first image we know the site was built from an ancient wordpress version and there will most likely be an exploit in the wild
~ from the second image we know all our users, nick and prisonmike.. we could run a bruteforce with wpscan against bost users, while we search if an exploit exists for the wp version ```echo -n "prisonmike\nnick >users.txt"

	Leading with the wp version we have a sensitive information disclosure exploit which could be carried out by appending the url with ```?static=1``` 

```
┌──(azazel㉿azazel)-[~/Hackthebox/paper]
└─$ searchsploit wordpress 5.2.3                          
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                                                                              |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
WordPress Core 5.2.3 - Cross-Site Host Modification                                                                                                                                                         | php/webapps/47361.pl
WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts                                                                                                                                     | multiple/webapps/47690.md
WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service                                                                                                                                                     | php/dos/47800.py
WordPress Plugin DZS Videogallery < 8.60 - Multiple Vulnerabilities                                                                                                                                         | php/webapps/39553.txt
WordPress Plugin iThemes Security < 7.0.3 - SQL Injection                                                                                                                                                   | php/webapps/44943.txt
WordPress Plugin Rest Google Maps < 7.11.18 - SQL Injection                                                                                                                                                 | php/webapps/48918.sh
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
                                                                                                                                                                                                                                              
┌──(azazel㉿azazel)-[~/Hackthebox/paper]
└─$ searchsploit -m multiple/webapps/47690.md;cat 47690.md
  Exploit: WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts
      URL: https://www.exploit-db.com/exploits/47690
     Path: /usr/share/exploitdb/exploits/multiple/webapps/47690.md
File Type: ASCII text

Copied to: /home/azazel/Hackthebox/paper/47690.md


So far we know that adding `?static=1` to a wordpress URL should leak its secret content

Here are a few ways to manipulate the returned entries:

- `order` with `asc` or `desc`
- `orderby`
- `m` with `m=YYYY`, `m=YYYYMM` or `m=YYYYMMDD` date format


In this case, simply reversing the order of the returned elements suffices and `http://wordpress.local/?static=1&order=asc` will show the secret content                                                                   
```

	With this we have a disclosed secret message which divulges the existence of a subdomain ```http://chat.office.paper/register/8qozr226AhkCHZdyY` for office.paper, I add that to my hosts files and visit, it leads to a chat system, so lets just create an account and login, it takes us to a group chat where the admin "dwight" created a bot "recyclops" to manage chats and other absurd functions:

![image](/assets/img/posts/hackthebox/paper/chat.png)

	Lets relate with recyclops in private hehee, after trying out wild things in a direct message, through directory traversal we are able to retrieve files and list directory contents, on reading the /etc/passwd we get all the users of the system:

![image](/assets/img/posts/hackthbox/paper/etc-passwd.png)

	After snooping around I find a juicy ".env" file in dwights home dir which contains environment variables which have been exported and contained within the file, including a password, now I'm hyped!

![images](/assets/img/posts/hackthebox/paper/env.png)
![image](/assets/img/posts/hackthebox/paper/creds.png)

Trying the credentials on ssh grants us a foothold.

### Initial Access
	Just grab the user flag in dwight's home dir, and lets make ourselves root!


### Race conditions; Root!
	After manually enumerating for privesc vectors and finding nothing, I swiftly scp my go-to privesc script, where I detect the system is susceptible to the polkit exploit, read more about it [here](https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/), grab a copy [here](https://raw.githubusercontent.com/Almorabea/Polkit-exploit/main/CVE-2021-3560.py), scp 

``` 
┌──(azazel㉿azazel)-[~/Utils]
└─$ scp paperpwn.py dwight@office.paper:/tmp                                                                                                                                                                                        1 ⚙
dwight@office.paper's password: 
paperpwn.py                                                                                                                                                                                                 100% 2427     2.8KB/s   00:00    
```
 or wget to the target and run the script, switch to the user "ahmed" or whatever you edit the user to in the script, this exploit gives us a user with unlimited privileges as it creates a user belonging to the wheel group(the wheel group is a "special" group with users who have individual elevated persmissions  to gain access to admin level commands and binaries) the wheel group is tied to the sudo group, with this we can switch to root with our password(un comment the password in the script and edit the line to string: "yourpassword") and provide the password, thus with lateral privilege elevation we become a sudoer and vertical privesc become root.

![image](/assets/img/posts/hackthebox/exploit-use.png)

![image](/assets/img/posts/hackthebox/root.png)



Thanks for the read, I would appreciate feedbackks or corrections also connections XD thru discord @Rami3l#8039 or ![linkedIn](https://www.linkedin.com/in/daniel-johnson-9a39aa215/), salaam.
