---
layout: posts
title:  ARM Change the world
author: huntershuai
---

ARM Change the world
====

Nowadays, mobile phone is familiar tool for us, and meanwhile, the core of mobile phone, ARM processor, has blown away the whole mobile world for its high performance and low power cost. it is so attractive for me to Know more about it's feature and architecture.


## ARM Family

<img src="/images/arm_learning/arm_evolution.jpg"/>



##CMISS

 ARM Cortex Microcontroller Software Interface Standard (CMSIS)
 
The ARM Cortex Microcontroller Software Interface Standard (CMSIS) is a vendor-independent hardware abstraction layer for the Cortex-M processor series with the following components:

<img src="/images/arm_learning/cmsis.png"/>


* CMSIS-CORE: API for the processor core, peripherals and the instruction intrinsics.
* CMSIS-Driver: Standard API for Ethernet, I2C, MCI, NAND Flash, NOR Flash, SPI, USART, USB etc.
* CMSIS-DSP: SIMD optimized DSP Library for fix-point (q7, q15, q31) and single precision floating-point (32-bit) data types.
* CMSIS-RTOS: Common API for Real-Time operating systems.
* CMSIS-Pack: XML based package description of the user and device relevant parts of a file collection that includes source, header, and library files, documentation, Flash programming algorithms, source code templates, and example projects.
* CMSIS-SVD: XML based description of the peripherals for debuggers.
* CMSIS-DAP: A USB to Debug Access Port protocol and reference firmware implementation. Can be used by pyOCD.


##ARM Cortex-M3
Here, take M3 as an example to show the detail info about ARM series.

###Special Register
- Program Status Registers (PSRs or xPSR)
- Interrupt Mask Registers (PRIMASK, FAULTMASK, and BASEPRI)
- Control Register (Control)

use MSR & MRS to access Special Register(only at privilege):
	
	MRS   <reg>, <special_reg>; Read special register
    MSR   <special_reg>, <reg>; write to special register


其实，为了快速地开关中断，CM3还专门设置了一条CPS指令，有4种用法 
   
	CPSID I  ;PRIMASK=1，  ;关中断 
  	CPSIE I  ;PRIMASK=0，  ;开中断 
  	CPSID F  ;FAULTMASK=1, ;关异常 
  	CPSIE F  ;FAULTMASK=0  ;开异常 

control register:
	
	CONTROL[0]: 0, privilege; 1, user
	CONTROL[1]: 0, MSP;       1, PSP


