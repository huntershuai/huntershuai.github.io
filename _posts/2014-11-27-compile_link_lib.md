---
layout: posts
title: Compile,Link and Lib 
author: huntershuai
---

Compile, Link and Lib Introduction
===

Tag: computer system


相关command记录:

1. readelf -s *.o  :产看.o文件符号表

2. ar -t libc.a :查看库中所包含的目标文件

3. ar -x libc.a : 解压库

4. gcc -static --verbose -fnobuiltin *.c : -verbose 将编译连接过程所有的过程打印出来，

5. ar -t xx.a   查看库文件信息




