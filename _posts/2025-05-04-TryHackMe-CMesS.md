---
title: "TryHackMe: CMesS"
categories:
  - TryHackMe
tags:
  - pwn
  - root
---
## Table of Contents

1. [Introduction](#introduction)
2. [Nmap Scan Results](#Nmap_Scan_Results)
3. [Enumerating HTTP](#Enumerating_HTTP)
6. [Privilege Escalation](#Privilege_Escalation)
7. [Root Flag](#Root_Flag)
9. [Conclusion](#Conclusion)

## Introduction

##  Nmap scan results
![Nmap_Scan](https://github.com/user-attachments/assets/f1e6287b-61a8-49c4-b385-8a1777c7a5a4)
Our Nmap scan shows that we have an apache website running as well as an open ssh port that we can connect to later; when we have credentials.


## Main webpage
![HomePage](https://github.com/user-attachments/assets/599b115e-c83c-46e6-ba64-7841f042cb23)
Checking out the website on port 80, we have a Gila CMS page. There does not seem to be any visible vulnerabilis here, I can further possible file paths and subdomains. 

## Domain/Subdomain enumeration
![Dirb](https://github.com/user-attachments/assets/f8f67bb0-b3fd-45f4-ad43-775b4779868a)

Using gobuster I find a myriad of file paths. After exploring these paths, I find that /admin page redirects to a login page. Currently I do not have any credentials for this page so we will have to explore further
![Subdomain_enum](https://github.com/user-attachments/assets/61596da1-c957-47ce-af84-63839be2b515)

Before I begin subdomain enumberation, I added cmess.thm to /etc/hosts using the command "sudo tee --append /etc/hosts <<< "<IP> cmess.thm". I used wfuzz to enumerate any possible subdomains, and I get a hit! dev.cmess.thm. After adding this to /etc/hosts and loading it to my browser I get a development page.
![dev_subdomain](https://github.com/user-attachments/assets/1ff58949-5329-4469-809a-536f40294cca)
This development page includes user and password credentials for user andre: andre@cmess : KPFTN_f2yxe% 

## Admin Panel
Returning back to the login page I can successfully login to the admin page using the given credentials
![admin_panel](https://github.com/user-attachments/assets/ffe8492f-0e56-4e57-bb74-37f9db5fcacd)
Looking around the admin page I find a password in the /admin/users page. This contains a password hash. I copied this hash into https://hashes.com/en/tools/hash_identifier, which returned a blowfish hash type. Unfortunitly this hash type is secure and would not be visable to brute force it.
![blowfish_hash](https://github.com/user-attachments/assets/6a13ee30-f684-4ca9-9241-6147b08f3afd)
On the main page, I am preseneted with a admin panel, exploring further I find a CMS version. This can be queried for any exploits
![cms_version](https://github.com/user-attachments/assets/77b864e4-4a85-47b0-99c4-928785739b40)

## Exploit
Searching online for "CMS version 1.10.9 exploits" I came across https://www.exploit-db.com/exploits/51569. 
![exploit_page](https://github.com/user-attachments/assets/b42bafc5-fdb6-402f-9941-13dc83d0b95d)
Once I downloaded the exploit, I changed its permissions using "chmod +x exploit.py" and ran the script.
![exploit_error](https://github.com/user-attachments/assets/a860e263-5c92-4142-9178-e575837724ef)
After running the script it gave me an "No module named 'term colour'" error. To fix this I used the command "sudo apt python3-termcolour". This allowed me to run the exploit.
![exploit_shell](https://github.com/user-attachments/assets/bb9c71d8-4020-464a-ab31-6f92b40f09c9)
Before I ran the exploit, I started a reverse listener using netcat (nc). The command I used was "nc -lvnp 4444". I ran the script again and successfully gained a shell with user www-data.

## Lateral Movement
![dir_user_owns](https://github.com/user-attachments/assets/029cc120-78a1-42e1-b9b9-2d8e7ca87385)
![rev_shell](https://github.com/user-attachments/assets/aee0260e-88e4-4a64-a0e7-fae0062fbbbf)

## Linpeas
![linpeas](https://github.com/user-attachments/assets/8526378c-4f50-45ca-bf35-4ddbd9864ba7)
![linpeas_bak](https://github.com/user-attachments/assets/be8a9467-c0e3-462c-a9f9-c5bb4c62770a)
![password_backup](https://github.com/user-attachments/assets/6b7f2d60-51f7-419e-9b3a-320408b76581)
![andre_ssh](https://github.com/user-attachments/assets/2336eda4-a503-4c2f-88a0-fde0d56971fe)


## Privilage Escalation 
![privEsc](https://github.com/user-attachments/assets/8c959b48-6341-4b99-aecb-894075369122)
![priv_esc](https://github.com/user-attachments/assets/fb6a58d4-cf19-4f74-bdd2-a6429dd7544c)
![Crontab](https://github.com/user-attachments/assets/5d456807-62cc-4f66-8d7b-86d39590f0ea)

## Root flag
![root_flag](https://github.com/user-attachments/assets/d705dc8e-0e82-481a-8330-f3dcb0697307)
