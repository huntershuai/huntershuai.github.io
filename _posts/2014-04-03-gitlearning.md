---
title: git learning 
layout: posts
author: huntershuai
---

git 学习笔记
======
使用git有一段时间了，之间总是有一些command很容易忽视和忘记，这里，通过这份笔记，将一些command纪录下来，同时顺便进一步学习下git的使用原理。


##git 文件管理
###忽略文件
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
		
