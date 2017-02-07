---
layout: posts
title: 程序员的自我修养 
author: huntershuai
tags:
- Computer
---

程序员的自我修养 
===
编译链接和库
--

Tag: computer system

<a href="http://book.douban.com/subject/3652388/">
<img src="/images/compile_link_lib/front_page.jpg" alt="compile link and lib"></img>
</a>

作者：俞甲子/石凡/潘爱民



相关command记录:

1. readelf -s *.o  :产看.o文件符号表

2. ar -t libc.a :查看库中所包含的目标文件

3. ar -x libc.a : 解压库

4. gcc -static --verbose -fnobuiltin *.c : -verbose 将编译连接过程所有的过程打印出来，

5. ar -t xx.a   查看库文件信息




###Computer System
<img src="/images/compile_link_lib/hardware_framework.png" width="400"/>

- 南桥(ISA):low speed bus

- 北桥(PCI):high speed bus
    
多道系统->分时系统->多任务系统  的演进



####线程调度与优先级
<img src="/images/compile_link_lib/thread_transition.png" width="400"/>  


###内存空间
linux 内存空间布局：
<img src="/images/compile_link_lib/linux_memory.png" width="400"/>  

- 栈
- 堆
- 可执行映像文件
- 保留区

####栈
- 返回地址
- 临时变量
- 上下文

一个function foo 分析：
	
	int foo()
	{
		return 123;
	}

反汇编：
	push ebp		
	mov ebp,esp		//1. store ebp,and new ebp point to current esp
	
	sub esp,0C0h    //2. prepare new stack space
	
	push ebx
	push esi
	push edi		//3. store: ebx,esi,edi

	lea edi,[ebp-0c0h]
	mov ecx,30h
	mov eax,0ccccccccch
	rep	stos dword ptr es[edi]	//4. add debug information

	mov eax,7Bh		//5. return value

	pop edi
	pop esi
	pop ebx			//6.restore edi,esi,ebx
	
	mov esp,ebp
	pop ebp			//7. restore esp,ebp
	
	ret				//8. ret return

**note**：单个字节的返回值存储在`eax`寄存器中。

####堆
堆空间频繁采用系统调用--低效

系统调用申请大块内存，由程序进行管理分配，提高效率--高效


#####linux 进程堆管理
两种堆空间分配方式：

- int brk（void *end_data_segment）

	设置进程结束段的地址

- void mmap（void *start,size\_t length,int prot,int flags, int fd, off\_t offset）
	参数含义依次为：起始地址，空间长度，空间权限（prot flags），指定文件描述符和文件偏移（fd,offset）
	
	mmap可以向系统申请一段虚拟地址空间，这段空间可映射到某个具体文件，当无具体文件映射时，这段地址空间为匿名空间，匿名空间可以作为堆来使用。
	
	__see detail in: linux manpage__


glibc 中的malloc函数：

mem size<128KB,在现有的堆空间中进行分配

mem size>128KB,调用mmap函数申请匿名空间

Linux kernel 内存分配状况：２.６

#####windows 进程堆管理

to be discussed


#####堆分配算法

######空闲链表

<img src="/images/compile_link_lib/available_list.png"/>

当请求一块内存a时，将一块大于a的内存分为两块，将该空闲块的指针更新为新的剩余空闲块的指针。如果为零，则直接删除原来的空闲块结点。

######位图

<img src="/images/compile_link_lib/heap_bit_map.png"/>

High：11 00 00 10 10 10 11 00 00 00 00 00 00 00 10 ：Low

eg:
HeapSize=1MB

BlockSize=128KB

BlockNumber=HeapSize/BlockSize=8K

8k/（32/2）=512 int表示



核心思想：
- 将堆分成若干个块
- 每个块标记成Head, Block, Free，可用两个bit区分标记
- 将满足申请size需求的整数个块分配给用户，首个块为Head,其余为Block

优缺点：

- 速度快：直接访问数组，空闲空间容易命中
- 稳定性好：可对数组做备份，防止用户修改，不造成整个heap无法使用
- 块冗余管理信息少，易于管理
- \__易产生内存碎片\__
- \__为了减少内存碎片，缩小块的大小，这时会造成位图数组很大，削弱速度快的优势（ps：可采用多级位图）\__

######对象池
核心思想：
假定每次申请的heap size一定，简化对象池中heap分配的过程

？？对象池之外，是否需要额外的管理，保证每次申请的heap size一定


###运行库
一个典型的程序运行大致步骤：

1. 操作系统创建进程，控制权交给程序入口（运行库中的某个函数）
2. 入口函数对运行库和程序运行环境进行初始化（堆，IO，线程，全局变量等）
3. 调用main
4. main结束后，返回入口函数，执行清理工作（全局变量析构，堆销毁，关闭IO等）

__note__:在嵌入式中，第一步操作系统的工作由Arm或51的内核完成，同时不创建进程。

####glibc入口函数



####c运行库（CRT）
c运行库的功能:

1. 启动与退出（入口函数及其依赖函数）
2. 标准函数
3. I/O：I/O功能封装
4. 堆：堆的封装和实现
5. 语言实现：语言中一些特殊功能实现
6. 调试：实现调试功能

。。。

###系统调用与API

对于Windows来说,系统调用即window的API（API间接调用系统接口）

#####Linux系统调用

x86架构下，由中断号`0x80`	中断完成

Linux下的系统调用接口：

- exit
- fork
- read
- write

可以直接绕过运行库中的对应接口而直接采用系统调用：

如使用`open(),read(),write()`等替换fopen()...

__系统调用的缺陷：__增加移植的困难，削弱兼容性

#####系统调用原理

计算机系统一般分为两种模式：内核模式（特权模式），用户模式
两种模式间的切换：
<img src="/images/compile_link_lib/cpu_int.png"/>  


i386下系统调用中断号：

- windows int 0x2e
- linux   int 0x80

多个系统调用通过同一中断号产生中断，由__系统调用号进行区分__

<img src="/images/compile_link_lib/linux_int_process.png"/>  


<img src="/images/compile_link_lib/linux_int_call.png"/>  

linux在不同模式下进行切换的同时，要对栈也进行同步切换，即从用户模式到内核模式时，要保存当前用户模式的栈参数。
- 切换到当前进程的内核栈（每一个进程都有自己的内核栈）
- 保存用户模式的寄存器至内核栈中

<img src="/images/compile_link_lib/stack_store.png"/>  

###编译
编译过程在整个生成可执行文件过程中的位置：
.c文件－>(预处理)-> .i文件 ->（编译）－> .s文件－>(assemble 汇编)->.o ->(linker 链接)－>.out

编译过程我的理解即将文本文件中的code翻译为计算机所理解的symbol的过程。
其中包括：

- 词法分析：扫描过程，lex程序完成，code被解析成分离的token
- 语法分析：Grammar Parser，生成语法树
- 语义分析：Semantic Analyzer，
- 中间代码生成：简化一些在编译期间即可确定的过程
- 目标代码生成与优化


###链接

对链接的理解需要统一静态链接与动态链接，两部分进行对比，在本书中，这两部分分为前后的两大块，相隔时间一长，比较起来不方便，容易看了后边忘了前边的实现细节，这边，把静态链接与动态链接（共享库）部分整合到一起，重新梳理下链接的整个过程以及技术演化的历史。

链接过程主要包括：重定位、地址和空间分配、符号决议（symbol resolution）
编译过程会为交互符号提供重定位入口（relocation entry）


首先是地址和空间分配过程，静态链接过程会将不同.o文件的相同段组合在一起，相同的段共用同一个段空间，

<img src="/images/compile_link_lib/link_1.jpg"/>  

随后会进行符号解析和重定位过程：
在编译阶段，外部symbol的地址是不确定的，以伪地址代替，在链接阶段，要对外部symbol的地址进行重定位。

在重定位过程中，处理不同文件的同名弱符号（多个同名强符号会报错），链接器会以common块的处理方式处理这种情况，即以同名symbol中类型占据空间较大的为标准，
弱符号的定义：未初始化的全局变量

####C++中链接问题

模板问题（模板膨胀）：通过将模板单独放入一个段中，相同的模板最终链接到同一个段中。

函数级别链接：将未使用的函数从最终生成的文件中剔除。

**构造和析构
    elf文件提供了两个独立的段执行main函数前的初始化工作（全局对象构造函数在main函数进行调用，析构函数在main函数之后被执行）
     .init 段
     .fint 段

####链接不同编译器的目标文件
保证ABI相同（application binary interface）：

1. 采用同样的目标文件格式；
2. 同样的符号修饰标注；
3. 变量的内存分配方式相同；
4. 函数的调用方式相同。

- c++的结构复杂造成其生成的二进制代码在不同的编译器下会产生较c更大的差异，移植起来更加复杂和艰难。

####动态链接，共享库





