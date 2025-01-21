---
title: "TryHackMe: Simple CTF"
categories:
  - TryHackMe
tags:
  - pwn
  - root
  - SQL Injection
---
## Table of Contents

1. [Introduction](#introduction)
2. [Nmap Scan Results](#Nmap_Scan_Results)
3. [Enumerating HTTP](#Enumerating_HTTP)
6. [Privilege Escalation](#Privilege_Escalation)
7. [Root Flag](#Root_Flag)
9. [Conclusion](#Conclusion)

## Introduction
![image](https://github.com/user-attachments/assets/5551444d-e04c-4454-b326-34a18418a123)

Beginner level ctf

##  Nmap scan results

```
└─$ nmap -sV 10.10.133.211
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-09 02:13 GMT
Nmap scan report for 10.10.133.211
Host is up (0.020s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
```

## Enumerating HTTP
I start by looking at the website.

![image](https://github.com/user-attachments/assets/1d50c079-dbaa-4a1c-8518-addd4f5604b1)

There is not much going on, so we will start to find other web pages on the website

## Gobuster
Using Gobuster we can find any hidden directories within the website
```
└─$ gobuster dir --url http://10.10.133.211/ --wordlist /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
===============================================================
 Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.133.211/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
 ===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/simple               (Status: 301) [Size: 315] [--> http://10.10.133.211/simple/]
```
We got a hit! Viewing /simple in our browser we are redirected to a new page:

![image](https://github.com/user-attachments/assets/783d5baa-0c0e-407b-a755-e566832dbef6)

Looking through this page I found the version number of simple.

![image](https://github.com/user-attachments/assets/ba14ea98-d4b2-489a-8ac2-e5770d218c33)

Searching "CMS Made Simple version 2.2.8" online, we find https://www.exploit-db.com/exploits/46635 CVE:2019-9053 which allows us to perform an SQL injection.

![image](https://github.com/user-attachments/assets/36647154-d557-4f60-ab53-7e10a3b01f29)

This indicates that there will be a login page which we will try to find now.
We can use Gobuster again, however, instead, I tried http://10.10.133.211/simple/login, with no result then http://10.10.133.211/simple/admin, which was successful and redirected me to a login page: 

![image](https://github.com/user-attachments/assets/f9dc964f-ef3a-450b-b133-551aeb6ffdde)


