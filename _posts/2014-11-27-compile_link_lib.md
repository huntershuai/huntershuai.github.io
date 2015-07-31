---
layout: posts
title: 程序员的自我修养 
author: huntershuai
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
- 空闲链表

<img src="/images/compile_link_lib/available_list.png"/>

当请求一块内存a时，将一块大于a的内存分为两块，将该空闲块的指针更新为新的剩余空闲块的指针。如果为零，则直接删除原来的空闲块结点。

- 