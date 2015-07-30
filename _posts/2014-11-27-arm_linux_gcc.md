---
layout: posts
title:  ARM Gcc
author: huntershuai
---


ARM GCC
===

Tag: embeded

###Source for download

<a href="https://launchpad.net/gcc-arm-embedded">https://launchpad.net/gcc-arm-embedded</a>

<a href="http://ftp.snapgear.org/pub/snapgear/tools/arm-linux/">http://ftp.snapgear.org/pub/snapgear/tools/arm-linux/</a>

<a href="http://www.arm9.net/download.asp/">http://www.arm9.net/download.asp </a>


###工具安装
这里选择图形化配置工具Ncurses：

Ubuntu Ncurses安装 （make menuconfig）：

<a href="http://www.linuxidc.com/Linux/2012-04/58746.htm
/">http://www.linuxidc.com/Linux/2012-04/58746.htm
 </a>

###arm-elf和arm-eabi的区别
arm编译器在gcc的一些概念

binutils+gcc+glibc = arm-linux-gcc 在以arm为处理器的linux系统

binutils+gcc+newlib = arm-elf-gcc 在以arm为处理器的裸系统

eabi = arm的fpu

none-eabi =arm无软件浮点,如stm32.

arm-linux-gcc和arm-elf-gcc有什么区别

比如printf这个函数，在arm-linux-gcc里是调用linux内核的syscall。
而在arm-elf-gcc里需要将完整的printf函数编译进你的程序里。
所以arm-elf-gcc可以编译linux内核，生成linux操作系统，然后用arm-linux-gcc去生成基于linux的应用程序。


