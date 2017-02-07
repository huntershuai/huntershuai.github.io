---
layout: posts
title: ARM Starup.s Analysis
author: huntershuai 
tags:
- ARM
---

ARM Starup.s Analysis
===


startup.s 文件主要完成三个工作：

1. 堆栈以及堆的初始化
2. 定位中断向量表
3. 调用Reset Handler


ARM Cortex-m0 startup.s

	Stack_Size      EQU     0x0
	
	                AREA    STACK, NOINIT, READWRITE, ALIGN=3
	Stack_Mem       SPACE   Stack_Size
	__initial_sp    EQU     0x20010000
	
	;// <h> Heap Configuration
	;//   <o>  Heap Size (in Bytes) <0x0-0xFFFFFFFF:8>
	;// </h>
	
	Heap_Size       EQU     0x0
	
	                AREA    HEAP, NOINIT, READWRITE, ALIGN=3
	__heap_base
	Heap_Mem        SPACE   Heap_Size
	__heap_limit
	
	
	                PRESERVE8                       ;below is 8 Bytes allign
	                THUMB							;below is THUMB code


	; Vector Table Mapped to Address 0 at Reset

                AREA    |.vectors_table|, DATA, READONLY, ALIGN=8
	__Vectors       
                DCD     __initial_sp                ; Top of Stack
                DCD     Reset_Handler             	; Reset Handler()
                DCD     NMI_Handler               	; NMI Handler
                DCD     HardFault_Handler         	; Hard Fault Handler
                DCD     MemManage_Handler         	; MPU Fault Handler
                DCD     BusFault_Handler          	; Bus Fault Handler
                DCD     UsageFault_Handler        	; Usage Fault Handler
                DCD     0                         	; Reserved
                DCD     0                         	; Reserved
                DCD     0                         	; Reserved
                DCD     0                         	; Reserved
                DCD     SVC_Handler               	; SVCall Handler
                DCD     DebugMon_Handler          	; Debug Monitor Handler
                DCD     0                         	; Reserved
                DCD     PendSV_Handler            	; PendSV Handler
                DCD     SysTick_Handler           	; SysTick Handler

                ; External Interrupts
                DCD     SysOnIntrHandle
                DCD     WdgIntrHandle
                DCD     Timer0IntrHandle
                DCD     Timer1IntrHandle
                DCD     Timer2IntrHandle
                DCD     BlueWizIntrHandle
                DCD     HciDmaIntrHandle
                DCD     BzdmaIntrHandle
                DCD     CrossBarIntrHandle
                DCD     Gpio0IntrHandle
                DCD     Gpio1IntrHandle
                DCD     LogUartIntrHandle
                DCD     DataUartIntrHandle
				...
	
	;jump to excute function 'Reset_Handler'
	Reset_Handler   
				PROC
                EXPORT  Reset_Handler     [WEAK] 
                IMPORT  SystemInit				   ;defined with C 
                IMPORT  __main					   ;main entry
                LDR     R0, =SystemInit
                BLX     R0
                LDR     R0, =__main
                BX      R0
                ENDP


PROC 指令：
>FUNCTION 指令标记函数的开始。 PROC 是 FUNCTION 的同义词。
>
>语法
label FUNCTION [{reglist1} [, {reglist2}]]

>其中：
reglist1
是由被调用方保存的 ARM 寄存器的可选列表。 如果 reglist1 不存在，并且调试器检查寄存器使用情况，则调试器假定正在使用 AAPCS。
reglist2
是由被调用方保存的 VFP 寄存器的可选列表。






EXPORT 指令：
>
EXPORT 指令声明一个符号，可由链接器用于解析不同的对象和库文件中的符号引用。 GLOBAL 是 EXPORT 的同义词。
语法
>
EXPORT {[WEAK]}

>EXPORT symbol {[attr}]}

>EXPORT symbol [WEAK{,attr}]

>其中：
symbol
是要导出的符号名称。 符号名区分大小写。 如果省略了 symbol，则导出所有符号。

>WEAK
仅当没有其他源导出另一个 symbol 时，才应将此 symbol 导入其他源中。 如果使用了不带 symbol 的 [WEAK]，则所有导出的符号都是处于次要地位的。

>attr
>
是下列项之一：
>
DYNAMIC
当源代码链接到动态组件中时，symbol 对于其他组件是可见的。

>PROTECTED
当源代码链接到动态组件中时，symbol 对于其他组件是可见的，但是不能由其他组件重新定义。

>HIDDEN
当源代码链接到动态组件中时，symbol 对于其他组件是不可见的。