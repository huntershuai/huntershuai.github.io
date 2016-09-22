---
layout: posts
title:  ARM Change the world
author: huntershuai
---

ARM Change the world
====

Nowadays, mobile phone is familiar tool for us, and meanwhile, the core of mobile phone, ARM processor, has blown away the whole mobile world for its high performance and low power cost. it is so attractive for me to Know more about it's feature and architecture.


## ARM Family

<img src="/images/arm_learning/arm_family.png"/>



## CMISS 

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


## ARM Cortex-M3
Here, take M3 as an example to show the detail info about ARM series.

### Architecture

<img src="/images/arm_learning/m3_arch.png"/>



### General Register

General register including 16 register:
R0~R15

and R15 is also called PC , R14 is called LR, and R13 is SP(PSP or MSP)




### Special Register
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

### Memeroy mapping

<img src="/images/arm_learning/mem_map.png"/>



#### Bit Band Operation

- 1MB map to 32MB region
- Must word-align
- realize atomic operation, cannot be interrupted
- Must defined use `volatile`(C compiler not suppor bit band operation)


	bit_word_addr = bit_band_base + (byte_offset x 32) + (bit_number × 4)
	
For example:

output data to Port A

- not use bit band:
	
		for (u16 cnt=0; cnt<512; cnt++)					for (u8 num=0; num<8; num++) 
				if ((Buffer[cnt]>>num)&0x01)		
					GPIOA->BSRR = 1;
				else					GPIOA->BRR = 1;

- use bit band:

		//PORTA address is 0x42400280 
		volatile U32 *pBuffer = ((u32*)0x22080000)
		U16 cnt = 512*8;
		while(cnt--)
		{
			(*((u32*) 0x42400280)) = *pBuffer++;
		}
		
**note**:

- Bit band operation is not supported when use DMA to access SRAM(Bit band is realize through ARM kernel, so it is only available with CPU)
- 
### Opreation Mode



	

