---
title: "TryHackMe: Agent Sudo"
categories:
  - Blog
  - TryHackMe
tags:
  - pwn
  - root
---

The Nmap scan returns 3 open ports
```
└─$ nmap -sV 10.10.83.69
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-07 17:34 GMT
Nmap scan report for 10.10.83.69
Host is up (0.080s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
```
I began by enumerating port 80 (HTTP) via the web browser

![image](https://github.com/user-attachments/assets/c1f4a4be-b945-4a8a-9799-75b6178b16f8)

Agent R is telling us to change our user-agent to our own codename. We can do this by changing the user-agent tag in burpsuite or using curl. For this I used curl with the user-agent "R".
```
└─$ curl -A R 10.10.83.69
What are you doing! Are you one of the 25 employees? If not, I going to report this incident
<!DocType html>
<html>
<head>
<title>Annoucement</title>
</head>
 <body>
<p>
Dear agents,
<br><br>
Use your own <b>codename</b> as user-agent to access the site.
<br><br>
From,<br>
Agent R
</p>
</body>
</html> 
```
This has yielded a differnt page to before! Through the fact agent R says "Are you one of the 25 employees?" makes me thing the agents are named alphabetically, e.g. Agent A, Agent B, Agent C, etc. We can attmept to change the user-agent to differnet characters of the alphabet, however I am going to use a bash script to automate this:
```
#!/bin/bash
for letter in {A..Z}; do
    curl -L -A $letter 10.10.83.69; echo "Letter: $letter"
done
```
Running and evaluating the results from the script, we can see that user-agent of Agent C gives a different repsonce to the others.
```
Attention chris, <br><br>
Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>
From,<br>
Agent R
```
We now know that their is user named: chris, and that his password is weak. We can try to brute-force the ftp login with username "chris". To do this I will be using Hydra Password Cracker
```
```



