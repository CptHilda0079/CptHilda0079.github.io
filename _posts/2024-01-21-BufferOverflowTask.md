---
title: "Exercise: Buffer overflow task"
tags:
  - buffer overflow
---
## Table of Contents

1. [Introduction](#introduction)
2. [Inspecting using GDB-GEF](#Nmap_Scan_Results)

## Introduction

## Inspection using gdb/gef

We can begin this buffer overflow task by inspecting the executable. For this, I will be using GDB with the GEF extension. Viewing the functions defined within the program, it is vulnerable as it uses the outdated strycpy function and is susceptible to buffer overflow attacks.

![image](https://github.com/user-attachments/assets/8e6e163e-af5b-4078-937e-446ebb14f79a)

Disassembling the main function will give us an idea of how the program functions:

![image](https://github.com/user-attachments/assets/d94915eb-6032-4ae9-9c9f-07d613598115)

## Finding RIP offset

We begin by finding the offset of the RIP. We do this by generating a pattern using GEF's pattern create function:

![image](https://github.com/user-attachments/assets/d9ffd409-6f84-47c7-b655-558e46314a2d)

This works by overwriting the RIP with a unique value, which we can query to find the offset. View the frame information and use the pattern offset function to find the offset of the overwritten RIP. In this case, we have yielded a value of 264 bytes. This means that our buffer is 256 bytes (264 bytes - 8 bytes for RBP) in size.

Now we can begin to generate our exploit; we will need a NOP sled, shellcode and a return value. We can first start by finding our return value. This ideally should be around the start of the buffer. We can see this by generating a pattern of "A"s to fill the stack and identify the top/bottom memory addresses. We can breakpoint the last instruction to see where the values are written on the stack pointer (RSP)

![image](https://github.com/user-attachments/assets/c3ca6c3c-9669-4d98-8ff4-0ccb063ee6f4)

![image](https://github.com/user-attachments/assets/a01eb897-6c97-44a7-a31e-a9c5292a2e25)

![image](https://github.com/user-attachments/assets/56fb0c32-a3b2-4e7b-a560-04adf1c5ed4d)

As we can see, the stack has been overwritten with a bunch of A values; using the command "x/24wx $rsp", we can view the top of the stack to find where the buffer begins. From this image, we can see that it begins at memory address 0x7fffffffdb60.
> [!NOTE]  
> x/24wx $rsp displays the RSP register as 24 words (4 bytes each) 

## NOP sled and Shellcode

![image](https://github.com/user-attachments/assets/366e9ac8-ce63-4295-87c1-06827cd27d1d)

To construct our payload, we need to begin with a NOP sled. A NOP sled is a sequence
of NOP (no-operational) instructions used to ”slide” the execution flow to our shellcode. The NOP instruction is a command that tells the computer to do nothing. Our NOP
instructions aim to ensure that our exploit does not fail by ensuring that the CPU reaches
the shellcode. The size of our NOP sled is related to the size of our shellcode. For example,
if our shellcode is 23 bytes, it must be 141 bytes long (buffer + RBP = 164 bytes) to
ensure enough space in the buffer + RBP for our shellcode instructions.

Shellcode
Shellcode is a set of instructions that executes commands to exploit a vulnerability in a
program. A basic example of shellcode could execute the /bin/sh binary. If this program is
misconfigured as SUID, we could execute /bin/sh to get a root shell.
## Crafting the payload

![image](https://github.com/user-attachments/assets/a13e976c-f8c4-4fa4-89fa-0845638d2418)

1) the first step for the payload is the return address, instead of using the absolute beginning of the buffer, I have decided to use the address x7fffffffdbe0 as it is close to the start, meaning that it is less susceptible to errors and will guarantee redirection to the NOP sled. Next, we need our shell code; you can find shellcode online easily; an example is https://www.exploit-db.com/exploits/46907
   
> [!WARNING]  
> Not all online shell codes work; you might have to try several different ones. Also, please ensure the shell code is specific to your computer architecture. Most of the time, it will be x64/x68_64!

2) Secondly we need a NOP sled, which consists of multiple x90 NO-OP instructions that smoothly redirect the return address value to our shellcode, the length of our NOP sled is (buffer_size - length(shellcode)). Finally, by putting this together and saving it to a file, we get the following:

![image](https://github.com/user-attachments/assets/9e37b290-0dcc-40bd-808d-419587862523)

![image](https://github.com/user-attachments/assets/02624359-6119-4354-87e0-d0924d7bd33e)

![image](https://github.com/user-attachments/assets/0ff2afc6-430d-4c09-bb36-4cd5d17121d7)

We have successfully exploited the buffer, executed our shellcode and spawned a shell.
