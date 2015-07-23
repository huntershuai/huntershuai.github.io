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

<img src="/images/data_structure/static_link_list.png"   />

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


- 循环队列：front队头指针，rear队尾指针 front=rear空队列

	<img src="/images/data_structure/cycle_queue.png"  alt="cycle queue"/>

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

- 队列的链式存储结构及实现

	就是线性表的单链表，只能尾进头出。


##chapter 05 string 

串是由0个或多个字符组成的有限序列，即字符串

unicode 16位二进制   ASCII 8位二进制
字符串大小比较：

     s="hap"<t="happy"
     s="happen"<t="happy"  //从不同的地方比较ASCII码的大小  e<y;
    
串最后“\0”不计入串长度


###KMP 朴素匹配算法

![kmp](/images/data_structure/kmp.png )

关键在于求next数组
	
	void get_next(String T, int *next)
	{
		int i,j;
		i=1;
		j=0;
		next[1]=0;
		while(i<T[0])  //T[0] T length
		{
			if(j==0||T[i]==T[j])
			{
				++i;
				++j;
				next[i]=j;
			}
			else
				j=next[j];
			
		}
	}


##chapter 06 Tree

Definiton:

n个结点的有限集，n=0时为空树，n>1，其余结点可分为
m(m>0)个互不相交的有限集。

**note**

1. 数据结构中的树只能有一个根节点。
2. m>0时，子树的个数没有限制，但一定是互不相交的。

* 结点拥有的子树数为结点的度。树的度为结点中度的最大值
* 树的深度（高度）：树的结点中最大层次
* 树中结点的各子树看成是从左至右是有次序的，不能互换的，则为有序树。


###树的存储结构
- 双亲表示法：

   双亲域，长子域

- 孩子表示法：

   方案一：每个节点维护一个指针域，指针域的长度为树的度。
          对于结点度相差很大的树，浪费空间。
   
   方案二：按结点度的大小分配。
          维护每个结点的度值，带来时间上的开销。
- 二叉树： 
	
	n个节点的集合，或者为空集，或者由一个根节点和两棵互不相交的分别称为根节点的左子树和右子树组成
	
	－ 结点度最大为2
	
	－左子树和右子树有顺序，不能颠倒；
	
	－即使结点只有一颗子树，也要区分左右子树。

	1.斜树
	
     只有左子树--左斜树，，只有右子树-右斜树
     线性表的结构可理解为树的特殊表现形式
    
    2.满二叉树
    
     所有分支结点都有左右子树，并且所有叶子结点都在同一层级上。
     在同深度的二叉树中，满二叉树的叶子结点最多，结点也最多
    
    3，完全二叉树
    对二叉树按层序进行编号，编号节点与同样深度的满二叉树节点编号相同
    按层序标号连续的二叉树
    >
     叶子结点只能出现在最下两层；
          最下层叶子一定集中在左部连续位置；
          倒数第二层，若有叶子，则集中在右部连续位置；
          同样结点数的二叉树，完全二叉树的深度最小。

- 二叉树存储结构

  由于完全二叉树定义严格，可用顺序结构来存储而二叉树
  
  非完全二叉树用顺序结构存储将带来空间浪费，可用二叉链表来存储
  
－ 遍历二叉树

 前序：根结点，左子树，右子树

<img src="/images/data_structure/pre_traversal.png" alt="pre_traversal"/>

	前序遍历算法：
     void preOrderTraverse(node T)
     {
          if(T==null)
          {
               return;
          }
          else
          {
               printf("%c",T->data);
               preOrderTraverse(T->lchild);
               preOrderTraverse(T->rchild);
          }
     }
     
     
 中序遍历   左子树-根结点-右子树

<img src="/images/data_structure/middle_traversal.png" alt="middle_traversal"/>

	中序遍历算法：
	void inOrderTraverse（node T）
	{
     	if(T==null)
     	{
          	return;
     	}
     	else
     	{
            inOrderTraverse（T->lchild）;
            printf("%c",T->value);
            inOrderTraverse(T->rchild);
     	}
	}
     
后序遍历：从左到右先叶子节点后结点遍历左右子树，最后根节点。

<img src="/images/data_structure/back_traversal.png" alt="back_traversal"/>

后续遍历算法：

	void backOrderTraverse（node T）
	{
       if(T=Null)
           return;
       else
     	{
          backOrderTraverse(T->lchild);
          backOrderTraverse(T->rchild);
          printf("%c",T->value);
     	}
	}	

层序遍历：根节点开始，从左到右访问

<img src="/images/data_structure/floor_traversal.png" alt="floor_traversal"/>


－ 推导二叉树：

   已知前序遍历和中序遍历序列，和已知后序遍历和中序遍历序列，可以唯一确定一棵二叉树.
  
－ 线索二叉树:
     
   加上遍历方法线索的二叉链表称为线索链表.这样把一个二叉树变成了一个双向链表,

－ 树，森林和二叉树的转换

<img src="/images/data_structure/tree_to_bin1.png" width="300"/>

<img src="/images/data_structure/tree_to_bin2.png" width="300"/>


森林转换成二叉树

<img src="/images/data_structure/forest_to_bin1.png" width="300"/>

<img src="/images/data_structure/forest_to_bin2.png" width="300"/>

<img src="/images/data_structure/forest_to_bin3.png" width="300"/>


- 赫夫曼树及其应用

	树的路径长度就是从树根到每一结点的路径长度之和


##查找

>关键字（key）是数据元素的某个数据项的值，若能唯一的标识一个记录，则称为主关键字（primary key）
     查找就是根据某个值，在查找表中确定一个其关键字等于给定值的数据元素（或记录）



 - 顺序查找：依次比对
 
	 设置哨兵的方式，当n很大时，同样可提高效率。
	 用while()比较方式代替for循环判定比较。
 
 - 有序表查找
 
	  折半查找（二分查找）：要求有序表。
	  
	  插值查找：适用于分布比较均匀的有序表。
	  斐波那契查找：利用斐波那契数列.

 -  线性索引查找
	 索引:把一个关键字与它对应的记录相关联
	 线性索引:将索引项集合组织为线性结构
      1.稠密索引:索引项按照关键码有序的排列


__二叉排序树__

二叉排序树进行中序遍历可得到有序序列。

结点的删除：
找到删除结点的前驱和后即来代替将要删除的结点。


__平衡二叉树__

是一种二叉排序树，每一个结点的左子树和右子树的高度差至多等于1.  用途，构建开销相对较小的二叉排序树。
平衡因子：左子树深度减去右子树深度。

<img src="/images/data_structure/balance_tree.png" width="300"/>

距离插入结点最近，切平衡因子（BF）的绝对值大于1的结点为根的子树，称为“最小不平衡树”

<img src="/images/data_structure/min_non_banlance_tree.png" width="300"/>


不平衡树调整为平衡树：

	发现BF大于等于二，则二叉树进行右旋（顺时针）。小于等于-2则进行左旋（逆时针）。若根节点与子节点BF符号不相同，则不能直接进行简单的左旋。


- 哈希表

	散列函数构造方法：

 	1.直接定址法：
                 
      要求事先知道表的分布情况，适合查找表较小且连续的情况。
          
    2.数字分析法：

      适合处理关键字位数比较大的情况，事先知道关键字的分布且关键字的若干位分布较均匀。
          
    3.平方取中法：（平方后取中间位数）适合位数小的关键字，不知道关键字的分布。
          
    4.折叠法：

	5.除留余数法（最常用）：
	
	处理冲突的方法：
	
	1.开放定址法：一旦发生冲突，就寻找下一个空的散列地址，只要散列表足够大，
	
	2.再散列函数法：使用多个散列函数
	
	3.链地址法：

	4.公共溢出区法：


##排序

__排序的稳定性__

若两个键值key1=key2相等，排序前key1领先key2，若排序后仍保持此次序不变，则称排序方法是稳定的，若排序后改变则为不稳定。

__内排序与外排序__

内排序：待排序记录全部放在内存中，

外排序：记录太多，整个排序过程需要内外存之间多次交换数据才行。

内排序：插入排序，交换排序，选择排序，归并排序。


- 冒泡排序

		 正规方法：

	     for(i=0;i<length;i++)
	          for(j=length-1;j>=i;j--)
	               {
	                     if(L[j]<L[j-1])
	                        swap(L[j],L[j-1]);
	                }
		 时间复杂度：O(n^2);

- 简单选择排序

		for(i=1;i<length;i++)
		min=i;
		for(j=i+1;j<length-1;j++)
		   {
		        if(L[j]<L[min])
		             min=j;
		   }
		if(i!=min)
		   swap(i,min);
		时间复杂度：O(n^2);性能上优于冒泡排序；

- 插入排序
		直接插入排序
	 
		void insertvalue(L[])
		{
		     for(i=1;i<length;i++)
		     {
		          int invar=L[i];
		          int index=i-1
		          while(index>=0&&L[index]>invar)
		          {
		`               L[index+1]=L[index];
		                 index--;
		          }
		          L[index+1]=invar;
		     }
		}
		时间复杂度：O(n^2/4)

- 希尔排序

		void shellSort(int []array)
		{
		     int length=sizeof(array);//n为待排序序列的长度
		     int increment=length;
		      while(increment>1)
		     {
		           increment=increment/3+1;
		          for(int i=increment;i<=length-1;i++)
		          {
		               if(array[i]<array[i-increment])
		               {
		                    int temp=array[i];
		                    for(j=i-increment;j>0&&temp<array[j];j-=increment)
		                         array[j+increment]=array[j];
		                    array[j]=temp;
		               }
		          }
		     }
		}


- 归并排序：

时间复杂度：O(nlogn) 空间复杂度：O(n+logn)

- 快速排序算法：

		void QuickSort(int left, int right, int arr[])
		{
		     int l=left;
		     int r=right;
		     int mid=(l+r)/2;
		     int pivot=array[middle];
		     int temp=0;
		          whlie(l<r) 
		          {
		               while(arr[l]<pivot)  l++;
		               while(arr[r]>pivot) r--;
		               temp=arr[r];
		               arr[r]=arr[l];
		               arr[l]=temp;
		               if(l>=r)  break;
		               if (pivot==arr[r])  l++;
		               if (pivot==arr[l])   r--;
		               
		          }
		          if(l==r)
		          {
		               l++;
		               r--;
		          }
		          if(left<r) QuickSort(left,r,array);
		          if(right>l) QuickSort(l,right,array);
		}


7、堆排序

堆排序（简单选择排序的升级）
堆：每个节点的值大于或等于其左右孩子节点的值，为大顶堆，。。。小顶堆。

时间复杂度O(nlogn)  空间复杂度：只需一个用来交换的暂存单元。由于记录的比较和交换式跳跃式进行，因此堆排序也是一种不稳定的排序方法。

