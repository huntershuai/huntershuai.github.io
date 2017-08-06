---
title: git learning 
layout: posts
author: huntershuai
tags:
- Git
- Computer
---

git 学习笔记
======
使用git有一段时间了，之间总是有一些command很容易忽视和忘记，这里，通过这份笔记，将一些command纪录下来，同时顺便进一步学习下git的使用原理。


## git 文件管理

### 忽略文件

通过创建.gitignore 文件，列出需要忽略的文件

cat .gitignore
	
	# 此为注释 – 将被 Git 忽略
	# 忽略所有 .a 结尾的文件
	*.a
	# 但 lib.a 除外
	!lib.a
	# 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
	/TODO
	# 忽略 build/ 目录下的所有文件
	build/
	# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
	doc/*.txt
	# ignore all .txt files in the doc/ directory
	doc/**/*.txt
	
另外，也可以通过command暂时移除某个文件:
	
	git rm --cached filename
		
## branch



创建一个新branch

	git branch branchName 
	
在各个branch及master之间进行切换
	
	git check out branchName/master
	
使用组合方式
	
	git checkout -b branchName
	

merge branch into master
	
* 首先切换到master

		git checkout master
	
* 开始merge branch 到master 分支
		
		git merge branchName

* 删除branch 分支
		
		git branch －d branchName
	
	
**注**：
[Git详解之三 Git分支](http://www.open-open.com/lib/view/open1328069889514.html)中详细说明了branch的原理和流程，讲解透彻
	
	

## 附： Git 和 SVN 的主要区别


- Git 是分布式的，SVN 不是：
- Git 对内容的存储单元是元数据，而SVN的存储单元是文件，因此对文件的操作上，最小的操作单元也就不同
- Git 维护一个本地的版本库，而SVN必须联网
- Git 的分支和SVN 不同，
	

## 参考
1. [git 官网](https://git-scm.com)
	