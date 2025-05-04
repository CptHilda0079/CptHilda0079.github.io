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
![admin_panel](https://github.com/user-attachments/assets/ffe8492f-0e56-4e57-bb74-37f9db5fcacd)
![andre_ssh](https://github.com/user-attachments/assets/2336eda4-a503-4c2f-88a0-fde0d56971fe)
![blowfish_hash](https://github.com/user-attachments/assets/6a13ee30-f684-4ca9-9241-6147b08f3afd)
![cms_version](https://github.com/user-attachments/assets/77b864e4-4a85-47b0-99c4-928785739b40)
![Crontab](https://github.com/user-attachments/assets/5d456807-62cc-4f66-8d7b-86d39590f0ea)
![dev_subdomain](https://github.com/user-attachments/assets/1ff58949-5329-4469-809a-536f40294cca)
![Dirb](https://github.com/user-attachments/assets/f8f67bb0-b3fd-45f4-ad43-775b4779868a)
![dir_user_owns](https://github.com/user-attachments/assets/029cc120-78a1-42e1-b9b9-2d8e7ca87385)
![exploit_error](https://github.com/user-attachments/assets/a860e263-5c92-4142-9178-e575837724ef)
![exploit_page](https://github.com/user-attachments/assets/b42bafc5-fdb6-402f-9941-13dc83d0b95d)
![exploit_shell](https://github.com/user-attachments/assets/bb9c71d8-4020-464a-ab31-6f92b40f09c9)
![HomePage](https://github.com/user-attachments/assets/599b115e-c83c-46e6-ba64-7841f042cb23)
![linpeas](https://github.com/user-attachments/assets/8526378c-4f50-45ca-bf35-4ddbd9864ba7)
![linpeas_bak](https://github.com/user-attachments/assets/be8a9467-c0e3-462c-a9f9-c5bb4c62770a)
![Nmap_Scan](https://github.com/user-attachments/assets/f1e6287b-61a8-49c4-b385-8a1777c7a5a4)
![password_backup](https://github.com/user-attachments/assets/6b7f2d60-51f7-419e-9b3a-320408b76581)
![priv_esc](https://github.com/user-attachments/assets/fb6a58d4-cf19-4f74-bdd2-a6429dd7544c)
![privEsc](https://github.com/user-attachments/assets/8c959b48-6341-4b99-aecb-894075369122)
![rev_shell](https://github.com/user-attachments/assets/aee0260e-88e4-4a64-a0e7-fae0062fbbbf)
![root_flag](https://github.com/user-attachments/assets/d705dc8e-0e82-481a-8330-f3dcb0697307)
![Subdomain_enum](https://github.com/user-attachments/assets/61596da1-c957-47ce-af84-63839be2b515)
