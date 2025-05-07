---
title: "TryHackMe: CMesS"
categories:
  - TryHackMe
tags:
  - cms
  - pwn
  - cronjob
  - root
---
## Table of Contents

1. [Introduction](#introduction)
2. [Nmap Scan Results](#Nmap_Scan_Results)
3. [Enumerating HTTP](#Enumerating_HTTP)
4. [Privilege Escalation](#Privilege_Escalation)
5. [Root Flag](#Root_Flag)
6. [Conclusion](#Conclusion)

## Introduction

##  Nmap scan results
![Nmap_Scan](https://github.com/user-attachments/assets/f1e6287b-61a8-49c4-b385-8a1777c7a5a4)

For my Nmap scan, I used the -A flag, which includes the following commands: OS detection (-O), Version detection (-sV), Script scanning (-sC, using default NSE scripts) and Traceroute. Analysing the results, I see that the host has an Apache website running and an open SSH port that we can connect to later, when we have credentials. 

## Main webpage
![HomePage](https://github.com/user-attachments/assets/599b115e-c83c-46e6-ba64-7841f042cb23)

Checking out the website on port 80 (http), we have a Gila CMS page. There is no visible vulnerability here, I can further explore possible file paths and subdomains to find further information. 

## Domain/Subdomain enumeration
![Dirb](https://github.com/user-attachments/assets/f8f67bb0-b3fd-45f4-ad43-775b4779868a)

To enumerate file paths, I use Gobuster with a standard directory (dir) scan. The Gobuster results yield a myriad of file paths. After exploring these paths, I found that the /admin page redirects to a login page. I do not have any credentials for this page, so we will have to explore further.

![Subdomain_enum](https://github.com/user-attachments/assets/61596da1-c957-47ce-af84-63839be2b515)

Before I begin subdomain enumeration, I added cmess.thm to /etc/hosts using the command "sudo tee --append /etc/hosts <<< "<IP> cmess.thm". I used wfuzz to enumerate any possible subdomains, and I got a hit! dev.cmess.thm. I get a development page after adding this to /etc/hosts and loading it into my browser.

![dev_subdomain](https://github.com/user-attachments/assets/1ff58949-5329-4469-809a-536f40294cca)

This development page includes user and password credentials for user andre: andre@cmess: KPFTN_f2yxe%. I can use this information to log in to the admin login page.

## Admin Panel
![admin_panel](https://github.com/user-attachments/assets/ffe8492f-0e56-4e57-bb74-37f9db5fcacd)

After successfully logging into the cms, I am redirected to an admin dashboard.

![blowfish_hash](https://github.com/user-attachments/assets/6a13ee30-f684-4ca9-9241-6147b08f3afd)

Looking around the admin page, I find a password on the /admin/users page. This contains a password hash. I copied this hash into https://hashes.com/en/tools/hash_identifier, which returned a Blowfish hash type. Unfortunately, this hash type is secure and would not be vulnerable to brute force it.

![cms_version](https://github.com/user-attachments/assets/77b864e4-4a85-47b0-99c4-928785739b40)

On the main page, I am presented with an admin panel. Exploring further, I find a CMS version. This can be queried for any exploits.

## Exploit
![exploit_page](https://github.com/user-attachments/assets/b42bafc5-fdb6-402f-9941-13dc83d0b95d)

Searching online for "CMS version 1.10.9 exploits", I came across https://www.exploit-db.com/exploits/51569. Once I downloaded the exploit, I changed its permissions using "chmod +x exploit.py" and ran the script.

![exploit_error](https://github.com/user-attachments/assets/a860e263-5c92-4142-9178-e575837724ef)

After running the script, it gave me a "No module named 'term colour'" error. To fix this, I used the command "sudo apt install python3-termcolour". This allowed me to run the exploit.

![exploit_shell](https://github.com/user-attachments/assets/bb9c71d8-4020-464a-ab31-6f92b40f09c9)

Before I ran the exploit, I started a reverse listener using netcat (nc). The command I used was "nc -lvnp 4444" in a seperate tab to recieve the reverse shell once uploaded. I ran the script again and successfully gained a shell with user: www-data.

![rev_shell](https://github.com/user-attachments/assets/aee0260e-88e4-4a64-a0e7-fae0062fbbbf)

## Lateral Movement
After gaining access to the default Apache system account (www-data) with a read shell, I want to download and run Linpeas to find any leaked credentials or vulnerabilities that can be used to gain an account with higher access/functionality.  

![dir_user_owns](https://github.com/user-attachments/assets/029cc120-78a1-42e1-b9b9-2d8e7ca87385)

To do this, I need to find a directory that I have permission to write into. I automated this process using the command: find . "-type d-user www-data -print | xargs -0 ls -ld". The directory returned was: "./cache/apache2/mod_cache_disk". Using "ls -ld," I can confirm that www-data owns this directory and has write permissions.

## Linpeas
To download Linpeas onto the victim machine, I set up a basic HTTP server on my local machine and downloaded it on the machine using wget. This is better described on https://github.com/peass-ng/PEASS-ng/blob/master/linPEAS/README.md. Once Linpeas is downloaded, I run it and look through the data.

![linpeas](https://github.com/user-attachments/assets/8526378c-4f50-45ca-bf35-4ddbd9864ba7)

/opt/password.bak is an interesting backup file. Viewing this file, I can see that it contains login information for user Andre. I can use this information to gain an interactive shell.

![password_backup](https://github.com/user-attachments/assets/6b7f2d60-51f7-419e-9b3a-320408b76581)

This information allows me to now log in to user andre through SSH on port 22.

![andre_ssh](https://github.com/user-attachments/assets/2336eda4-a503-4c2f-88a0-fde0d56971fe)

## Privilege Escalation 
![Crontab](https://github.com/user-attachments/assets/5d456807-62cc-4f66-8d7b-86d39590f0ea)

After searching around the system for privilege esc vulnerabilities, I found a suspicious cron job running. Searching for /tar on GTFO bins https://gtfobins.github.io/gtfobins/tar/ I find this command: "tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh".

![privEsc](https://github.com/user-attachments/assets/8c959b48-6341-4b99-aecb-894075369122)

I can exploit this by changing the directory to /home/andre/backup and creating a file called shell.sh. This program contains code that copies bash into a temp location, then gives the tmp/bash suid permissions. I then gave the file executable permission using "chmod +x shell.sh" and used the commands given by gtfobins.

![priv_esc](https://github.com/user-attachments/assets/fb6a58d4-cf19-4f74-bdd2-a6429dd7544c)

After waiting 1-2 minutes for the cronjob to execute, a file called /bash (red) should spawn in the /tmp directory. All that is left to do now is to run the file using "./bash -p". This, if successful, should invoke a root shell.

## Root flag
![root_flag](https://github.com/user-attachments/assets/d705dc8e-0e82-481a-8330-f3dcb0697307)

Success!
