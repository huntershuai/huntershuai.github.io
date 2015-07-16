---
title: 数据结构
layout: posts
author: huntershuai
---

《大话数据结构》，一本介绍常用数据结构的工具书，在毕业找工作时，一些容易遗忘的数据结构，通常随手在这本书中找到答案，并很快回忆起书中相关的代码实现方法，在笔试时起到了不小的作用。今天，重新整理下之前的笔记。

@home 

##chapter 01
首先是一些基本概念：

* 数据：计算机操作的对象

* 数据元素：组成数据的基本单位。 
 
* 数据项：数据元素可由若干个数据项组成

* 数据对象：性质相同的数据元素的集合。数据的子集

* 数据结构：数据关系

|数据              |
|:--------------: |
|数据对象			   |
|数据元素|数据元素|数据元素
|数据项  |数据项  |数据项

逻辑结构：

*  集合
*  线性
*  树形
*  图形

物理结构：数据的逻辑结构在计算机中的存储形式

* 顺序存储
* 链式存储 

##chapter 02: Alogrithm
推导大O阶：（时间复杂度）

1. 用常数1取代运行时间中的所有加法常数

2. 在修改后的运行函数中，只保留最高阶项

3. 如果最高阶项存在且不是1，则取除与这个项相乘的常数

复杂度的大小关系：

>O(1)<O(logn)<O(nlogn)<O(n^2)<O(n^3)<O(2^n)<O(n!)<O(n^n)

##chapter 03 线性表



###  顺序存储结构：数组实现

时间复杂度：O(n)

优点：更多的是存取数据的应用。可以快速的存取，无须为表中的元素逻辑关系增加额外的存储空间。

缺点：不适合元素变化不大的情况；线性表长度过大，难以确定存储空间容量；早场空间碎片。
线性表存储结构：

	typedef struct
     {
          int data[max_size];
          int length;
     }sqList;


###链式存储结构

结点（node）: value & pointer

头指针和头结点的区别：

* 头指针--指针，指向第一个结点
* 头指针--常用于标识链表
* 头指针为链表必要元素，始终不为空
* 头结点的作用主要体现在使得对链表的**每个节点**操作（`insert`,`delete`）得到统一

**单链表**
	
	typedef struct node
	{
	   struct node *next;
	   int value;
	}
	type struct node *linkList;
* 顺序存储
 
	查找：时间O(1)
 
	插入和删除：时间O(n)

* 链式存储-->

	查找：时间O(n)
	插入和删除：时间O(1)

**note**：顺序存储的查找复杂度可以理解为在一个固定的已知区域进行查找，因此为O(1),而链式查找则需要依次遍历各个结点的位置（memory address）因此综合下来为O(n).而对于插入删除操作，前者需要移动多个结点进行插入与删除，平均下来时间复杂度为O(n),而后者在确定位置后，可即刻实现插入,时间复杂度为O(1).


**静态链表**

<img src="~/images/data_structure/static_link_list.png"   />

	 静态链表的存储结构：     
     typedef struct
     {
          int data;
          int cur;  //游标，为0时表示无指向
     }component，staticLinkList[maxsize];

用数组代指针来描述单链表

* 优点：插入和删除时只需要修改游标，不需移动元素；？？
* 缺点：表长不确定，

**循环链表**

从表中一个节点出发，可以访问到链表中所有节点。

在链表上实现将两个线性表（a1，a2，…，an）和（b1，b2，…，bm）连接成一个线性表（a1，…，an，b1，…bm）的运算。

p=rearA->next;

reaA->next=rearB->next->next;  

reaB->next=rearA->next;

free(p);

**note**这边指定的是尾指针，而非头指针

**双向链表**

     typedef struct
     {
          int value;
          struct node *next;
          struct node *prior;
     }node, 
     两个指针域，指向前一个结点和后一个结点。

##chapter 04 stack & queue

     栈的数据顺序存储结构
     typedef struct
     {
          int data[max_size];
          int top;
     }sqStack;

     两栈共享空间数据存储结构：
     typedef struct
     {
          int data[max_size];
          int top1;
          int top2;
     }sqDoubleStack;

###stack

不存在线性表的插入和删除问题，缺陷是必须实现确定数组存储空间的额大小。

- 两栈共享空间

	一个指向栈顶，一个指向栈底，同时向中间靠拢。
	
	适用于：如买卖股票，一个买入，一个卖出，同时增加和减少的情况。

- 栈的链式存储结构及实现

	不存在栈满的情况，头指针为栈顶指针。

- 栈的应用：

	**递归**

	斐波那契数列实现1，1，2，3，5，8，13.。。

	使用递归时注意：递归定义必须有一个条件：满足递归时不再运行，不再引用自身而是返回值退出。

	递归过程退回的顺序是它前行顺序的逆序，符合栈的结构

	**四则运算表达式求值**

	后缀表达式（逆波兰法）可用于求解leetcode上关于简单字符串计算器问题

	[百科](http://baike.baidu.com/link?	url=C89qXxFjYPGbz73XP4qj5VcXOhdxA4uFgnheEaC2xv5gZnjtvXb8aC_QxkiQakGJaZsIaPxppIrP2fcH7LPCFq)
>不包含括号，运算符放在两个运算对象的后面，所有的计算按运算符出现的顺序，严格从左向右进行（不再考虑运算符的优先规则，如：(2 + 1) * 3 ， 即2 1 + 3 *

###queue

插入在队尾，删除在队头

![alt cycle queue](/images/data_structure/cycle_queue.png"cycle_queue")

<img src="~/images/data_structure/cycle_queue.png" width="400" alt="cycle queue"/>

循环队列：front队头指针，rear队尾指针 front=rear空队列

为避免删除数据时整个队列移动，将front指针不指向数组0元素，这样带来了新的问题：当插入新元素时，rear指针可能在队列未溢出时指向数组外，造成假溢出。

注意：循环队列在判定队列满或空时需要两个条件：front=rear，和另外一个自设条件。

循环队列的存储结构

	typedef struct
	{
     int data[maxsize];
     int front;
     int rear;
	}sqQueue;

插入队列操作：
（rear-front+QueuseSize）%maxsize;//计算队列长度的计算公式

	void insert(int value, sqQueue q)
	{
     	if((q->rear+1)%maxsize==q->front)
          	printf("error");
     	else
     	{
          	q->data[rear]=value;
          	q->rear=(q->rear+1)%maxsize;
     	}
	}

出队列操作：

	int out(sqQueue q)
	{
      	if((q->rear+1)%maxsize==q->front)
          	printf("error");
     	int e=q->data[front];
     	q->front=(q->front+1)%maxsize;
     	return e;
	}

队列的链式存储结构及实现

就是线性表的单链表，只能尾进头出。


##chapter 05 string 

串是由0个或多个字符组成的有限序列，即字符串

unicode 16位二进制   ASCII 8位二进制
字符串大小比较：

     s="hap"<t="happy"
     s="happen"<t="happy"  //从不同的地方比较ASCII码的大小  e<y;
    
串最后“\0”不计入串长度


###KMP 朴素匹配算法

![kmp ](~/images/data_structure/kmp.png)

关键在于求next数组





