---
layout: posts
title: markdown study
tags:
- Tool
---
markdown学习
=============
2014.07.19 SuZhou
_____________________
##第一次markdown亲密接触
前些天想了下如何通过jekyll 在github page上进行文字排版，今天重新打开阮一峰的blog，转到其提供的blog参考链接，发现了原来可以抛弃传统的排版思路，通过markdown实现代码式的排版，这有点类似于之前使用过的LaTeX。经过一番尝试，发现其语法结构要比LaTeX简单许多，让我们可以更加专注于文字的表达，顿感相见恨晚，So nice！

* 列表的使用

    1. 如果列表项目间用空行分开，在输出 HTML 时Markdown 就会将项目内容用`<p>`标签包起来，举例来说:

        	*   Bird

        	*   Magic
     会被转换为
        
        	<ul>
        	<li><p>Bird</p></li>
        	<li><p>Magic</p></li>
        	</ul>

    2. 列表可实现嵌套

* 链接
	1. 行内式
	
		要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可，例如：

			This is [an example](http://example.com/ "Title") inline link.

			[This link](http://example.net/) has no title attribute.
		转换为
	
			<p>This is <a href="http://example.com/" title="Title">
			an example</a> inline link.</p>

			<p><a href="http://example.net/">This link</a> has no
			title attribute.</p>
	2. 参考式
		
		参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

			This is [an example][id] reference-style link.
		你也可以选择性地在两个方括号中间加上一个空格：

			This is [an example] [id] reference-style link.
		接着，在文件的任意处，你可以把这个标记的链接内容定义出来：

			[id]: http://example.com/  "Optional Title Here"
* 代码
	
	1. 行内代码
		
		如果要标记一小段行内代码，你可以用反引号把它包起来（`），例如：

			Use the `printf()` function.
		会产生：

			<p>Use the <code>printf()</code> function.</p>

	**note** :
	如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段：
			
		``There is a literal backtick (`) here.``

* 图片

	行内式的图片语法看起来像是：

		![Alt text](/path/to/img.jpg)

		![Alt text](/path/to/img.jpg "Optional title")
	详细叙述如下：

	* 一个惊叹号 !
	* 接着一个方括号，里面放上图片的替代文字
	* 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上 选择性的 'title' 文字。
	
	参考式的图片语法则长得像这样：

		![Alt text][id]
 	
	* 「id」是图片参考的名称，图片参考的定义方式则和连结参考一样：

		[id]: url/to/image  "Optional title attribute"

	**note:** 到目前为止， Markdown 还没有办法指定图片的宽高，如果你需要的话，你可以使用普通的 <img> 标签。
