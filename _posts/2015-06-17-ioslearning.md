---
title: iOS journey 
layout: posts
author: huntershuai
---


iOS journey 
========
This is a collection of ios learning experience and knowleadge, and will be updated in the coming days.
2015.06.17 @ suzhou


###ios framework
1.core os

- osx kernel, 

- power management,

- Mach 3.0, 

- file system 

- ...

2.core services

- collections, 

- core location

- networking

- SQLite

- threading

- preference

- net services

- (add more later...)


3.Media

- core audio

- jpeg, png, tiff,...

- openAL

- openGL ES

- (add more later...)

4.cocoa touch

- multi touch

- alert

- core motion

- web view

- view hierarchy

- controls

- (add more later ...)


###MVC
model view controller

<img src="/images/ios/mvc.png" width=500 alt="mvc introduction" ></img>





###objective c
`@property(strong/weak,nonautomic)`

__strong__:release memory until all reference pointer set to nil or change to other object.
__weak__: will not retain the object memory , and will be set to nil if no one else  has a stong  pointer  to this object.

there is an example:

	@property (nonatomic, strong) NSString *string1;   

	@property (nonatomic, strong) NSString *string2; 
	@synthesize string1;   

	@synthesize string2;   
	self.string1 = @"String 1";   

	self.string2 = self.string1;   

	self.string1 = nil;  
	
	NSLog(@"String 2 = %@", self.string2);  
	
	==>output:String 2 = String 1

 but
 
 	@property (nonatomic, strong) NSString *string1;   

	@property (nonatomic, weak) NSString *string2; 
	@synthesize string1;   

	@synthesize string2;   
	self.string1 = @"String 1";   

	self.string2 = self.string1;   

	self.string1 = nil;  
	
	NSLog(@"String 2 = %@", self.string2);  
	
	==>output:String 2 = nil
 


###xcode 
Add this phase to list some problem when using xcode as part of ios learning.

1.

>directory not found for option '-L...'

>clang: error: linker command failed with exit code 1 (use -v to see invocation)


2.

LLVM:low level virtual machine

Clang

llvm 与clang的介绍：
<a href="https://linuxtoy.org/archives/llvm-and-clang.html">https://linuxtoy.org/archives/llvm-and-clang.html</a>

wiki:
>Apple 使用 LLVM 在不支持全部 OpenGL 特性的 GPU (Intel 低端显卡) 上生成代码 (JIT)，令程序仍然能够正常运行。之后 LLVM 与 GCC 的集成过程引发了一些不快，GCC 系统庞大而笨重，而 Apple 大量使用的 Objective-C 在 GCC 中优先级很低。此外 GCC 作为一个纯粹的编译系统，与 IDE 配合很差。加之许可证方面的要求，Apple 无法使用修改版的 GCC 而闭源。于是 Apple 决定从零开始写 C family 的前端，也就是基于 LLVM 的 Clang 了。












