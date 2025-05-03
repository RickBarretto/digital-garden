---
title: Operating Systems
draft: false
tags:
  - os
  - history
  - cs
  - status/🌱
  - literature
source: Modern Operating Systems
authors:
  - Tanenbaum, A.
  - Bos, H.
aliases: 
  - OS
description: My notes about Operating Systems
date: 2025-04-21
---

## What is an OS?

An operating System is an intermediate layer that deals with applications (user mode) and the hardware. This may be seen by two different perspectives: top-bottom or bottom-top. This system may be interpreted as an abstraction of the hardware, or a system able to manage resources and give essential features.

## Objective
- Abstract the hardware for programmers and applications (Top-bottom)
	- Have direct access to the hardware
	- Abstract giving to the programmer easier to deal APIs
- Manage resources: CPU, Memory, I/O (Bottom-up)
	- Allows security and performance multiplexing resources, such as Time and Space.
		- Time multiplexing: CPU sharing
		- Space multiplexing: Memory sharing

## Characteristics 
- Are, generally, large, complex and long life-time
	- Linux may have +40mi of lines
	- Windows about of +70mi of lines
	- Some exceptions are Minix (micro-kernel), with +15k lines
- **Fundamental Features**
	- Process management
		- Parallelism, Concurrency, Resources 
	- Memory management
		- Virtualization, Segmentation
	- Device management
		- Interface with external devices
	- I/O management
		- Persistency, Consistency, Resiliency
	- Security
		- Information Cryptography, Isolation, Sharing
		- Protocols
	- UI/UX
		- Differential of marketing
		- See MacOSX: beautiful, easy to use, delivers the best experience to the final user
- **Structure**
	- Kernel
	- System calls (syscalls)
	- Programming Interface (API)
	- User Interface (UI)
- **Category**
	- Monoprogrammable
	- Multiprogrammable
	- Real Time
	- Distributed
	- Networking
	- Time-Shared

## Threads and Process
- **Process:** an executing program with its own and independent resources
	- Allows multitasking with independent resources
- **Thread:** also called light-process, basic unit of execution
	- Allows multitasking sharing the same memory resources
	- Can lead to racing-conditions

## Modern Trends
- Desired features:
	- Security
	- Energy efficiency 
	- New architectures support, such as RISC-V
- Trends:
	- Microkernels: *MINIX*
	- Virtualization: *Hyper-V*
	- Mobile devices: *Android*, *iOS*
	- AI company: *Win11*, *iOS*

## Computer Generations
- **0th**
	- No OS
	- Calculators machines and mechanical computers
	- Examples: Blaise Pascal (1642), Leibniz (1672), Babbage (1822 and 1837)
- **1st**
	- No OS
	- Programming made in machine code or connecting circuits or valvules
	- Examples: ABC, Z3, Colossus (1944), Mark I, ENIAC
- **2nd**
	- Introduction to transistors
	- Mainframes
		- Could be manufactured
			- Limited usage due to expensiveness
			- Restricted to big corporations, universities or government agencies
	- Usage of Programming languages as **FORTRAN** or Assembly
		- The programmer could write the code into a paper (FORTRAN) or Assembly
		- The language would be translated into punched cards
		- The output was pressed off-line
	- Operating Systems: FORTRAN Monitor System (FMS), IBSYS (IBM 7094)
- **3rd**
	- Hardware Innovations
		- Introduction to Integrated Circuits
	- OS Innovations
		- Introduction to multiprogramming
		- Introduction to time-sharing concept: Each user has its own on-line terminal to access the system
	- Industry Innovations
		- Commercial and Scientific usage
	- Operating Systems: 
		- OS/360 (by IBM)
		- CTSS (Compatible Time Sharing System by MIT)
		- MULTUCS (MULTplexied information and Computing Service)
		- UNIX, by AT&T's Bell Labs
		- System V, by AT&T
		- BSD (Berkeley Software Distribution): the first Open Source UNIX
		- MINIX, UNIX-like system created by Tannenbaum for educational purposes
- **4th**
	- VLSI (Very Large Scale Integration) introduction
	- First personal computers
		- Microcomputers
		- Networking
	- Operating Systems:
		- CP/M (Control Program for Microcomputers)
		- DOS (Disk Operating System)
		- MS-DOS (MicroSoft DOS)
		- MacOSX, by Apple, based on UNIX
		- FreeBSD, based on BSD
		- Windows (97, 98, NT), by Microsoft
- **5th**
	- Focused on mobile, distributed, networking, real time, embedded systems...