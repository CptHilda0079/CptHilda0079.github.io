---
title: "Exercise: Buffer overflow task"
tags:
  - buffer overflow
---
## Table of Contents

1. [Introduction](#introduction)
2. [Inspecting using GDB-GEF](#Nmap_Scan_Results)

## Introduction

We can begin this buffer overflow task by inspecting the executable. For this, I will be using GDB with the GEF extension. Viewing the functions defined within the program, it is vulnerable as it uses the outdated strycpy function and is susceptible to buffer overflow attacks.

![image](https://github.com/user-attachments/assets/8e6e163e-af5b-4078-937e-446ebb14f79a)

Disassembling the main function will give us an idea of how the program functions:

![image](https://github.com/user-attachments/assets/d94915eb-6032-4ae9-9c9f-07d613598115)

We begin by finding the offset of the RIP. We do this by generating a pattern using GEF's pattern create function:
![image](https://github.com/user-attachments/assets/d9ffd409-6f84-47c7-b655-558e46314a2d)

then we view the frame information and use the pattern offset function to find the offset of the overwritten RIP.
In this case, we have yielded a value of 264 bytes. This means that our buffer is 256 bytes (264 bytes - 8 bytes for RBP) long.

Now we can begin to generate our exploit, for this, we will need a Nopsled, shellcode and a return value. We can first start by finding our return value. This ideally should be around the start of the buffer. 

![image](https://github.com/user-attachments/assets/1ca8e842-0d90-4a67-afd2-18b90abbf8bb)
