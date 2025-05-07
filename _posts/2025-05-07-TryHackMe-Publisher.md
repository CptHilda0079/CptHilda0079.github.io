---
title: "TryHackMe: Publisher"
categories:
  - TryHackMe
tags:
  - pwn
  - root
---
## Table of Contents

1. [Introduction](#Introduction)
2. [Nmap Scan Results](#Nmap_Scan_Results)
3. [Main Webpage](#Main_Webpage)
4. [Domain/Subdomain Enumeration](#Domain/Subdomain_Enumeration)
5. [Admin Panel](#Admin_Panel)
6. [Exploit](#Exploit)
7. [Lateral Movement](#Lateral_Movement)
8. [Linpeas](#Linpeas)
9. [Privilege Escalation](#Privilege_Escalation)
10. [Root Flag](#Root_Flag)

## Introduction

![Screenshot from 2025-05-05 23-21-31](https://github.com/user-attachments/assets/90790b8f-67eb-43ef-9bd5-c2fe8597d143)
![Screenshot from 2025-05-05 20-51-14](https://github.com/user-attachments/assets/eb96f031-a7db-4b40-868b-ccb7d1261c0e)
![Screenshot from 2025-05-05 20-13-42](https://github.com/user-attachments/assets/118d9e0a-9974-48d5-a2f0-a95cf0373b20)
![Screenshot from 2025-05-05 19-39-02](https://github.com/user-attachments/assets/dcf769f6-d927-4793-a10c-342a67ff627e)
![Screenshot from 2025-05-05 18-36-05](https://github.com/user-attachments/assets/9292e912-e69a-4d09-8732-3ce1409faea1)
![Screenshot from 2025-05-05 14-40-34](https://github.com/user-attachments/assets/3f2d4daf-73e7-415d-91dc-821f3b51403d)
![Screenshot from 2025-05-05 14-40-20](https://github.com/user-attachments/assets/6922904c-4d61-4b65-a36e-9ec909b3faf0)
![Screenshot from 2025-05-05 14-28-02](https://github.com/user-attachments/assets/7dab5a85-ec18-4542-9f92-519b8575b440)
![Screenshot from 2025-05-05 14-17-07](https://github.com/user-attachments/assets/258351d4-2690-4a10-bf78-621c6e180337)
![Screenshot from 2025-05-05 14-16-31](https://github.com/user-attachments/assets/92553f9d-800e-424c-a85d-e00d56957a7a)
![Screenshot from 2025-05-05 14-13-48](https://github.com/user-attachments/assets/3053e7f9-80d2-4501-b70a-833a11a6c2c6)
![Screenshot from 2025-05-05 14-13-21](https://github.com/user-attachments/assets/3b4f9cd9-354e-46a9-990b-1158ec4a71bf)
![Screenshot from 2025-05-05 14-12-07](https://github.com/user-attachments/assets/34804335-60c7-467c-a542-951e881526e9)
![Screenshot from 2025-05-05 13-50-07](https://github.com/user-attachments/assets/bd79ff84-c2f2-4472-b505-a90b8d0c5038)
![Screenshot from 2025-05-05 13-49-56](https://github.com/user-attachments/assets/bb8ee172-beb3-4397-a1a8-4c695176fea7)
![Screenshot from 2025-05-05 13-49-41](https://github.com/user-attachments/assets/be48a290-e62f-4488-aa41-a62526da7507)
![Screenshot from 2025-05-05 13-45-54](https://github.com/user-attachments/assets/ac91dd80-e784-48fc-aeaa-84edb9094cee)
![Screenshot from 2025-05-05 13-45-37](https://github.com/user-attachments/assets/53104f5a-7623-4e8c-9f8c-6fb6337373ec)
![Screenshot from 2025-05-05 13-34-00](https://github.com/user-attachments/assets/9cfa9a0a-456d-4d45-a22e-666e8d5b6331)
![Screenshot from 2025-05-05 13-33-38](https://github.com/user-attachments/assets/1bf9d6fd-88d3-4e46-ae9d-72d8e336b05f)
![Screenshot from 2025-05-05 13-27-56](https://github.com/user-attachments/assets/babc0fa0-4479-4c3f-8c58-4b1229451c53)
![Screenshot from 2025-05-05 13-27-35](https://github.com/user-attachments/assets/272dca0a-b4ea-4793-8814-ab93b0bc3a3c)
