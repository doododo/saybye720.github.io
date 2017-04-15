---
layout:		post
title:		Mac下常用的SVN命令及报错
description: 在Windows环境中，我们一般使用TortoiseSVN来搭建svn环境。在Mac环境下，由于Mac自带了svn的服务器端和客户端功能，所以我们可以在不装任何第三方软件的前提下使用svn功能
category:	blog
---

## 检出根目录下所有文件

SVN Checkout 不包括源文件夹根目录，比如我要checkout   trunk/ 下面的所有文件，但是不包括trunk 文件夹
我们可以在svn文件夹后面打个空格，在加个“.”就行了
eg:

	svn co http://192.168.1.10/svn/project/trunk/
	改为
	svn co http://192.168.1.10/svn/project/trunk/ .

## checkout

	svn checkout svn地址，
	简写：
	svn co svn地址

## 添加文件

	svn add test.txt 添加单一文件
	svn add *.* 添加所有文件
	svn add *.php 添加所有后缀为php的文件

## 提交文件

	svn ci -m “描述（可留空）” 文件名 提交单一文件
	svn ci -m “描述（可留空）” 提交所有文件

## 删除文件

	svn delete 文件名
	简写：
	svn (del, remove, rm)

## 加锁/解锁

	svn lock -m “LockMessage“ [--force] PATH
	svn unlock -m “LockMessage“ [--force] PATH

## svn update，简写 svn up

	svn update 更新所有文件
	svn update -r 200 test.php (将版本库中的文件test.php还原到版本200)
	svn update test.php (更新单一文件，于版本库同步。如果在提交的时候提示过期的话，是因为冲突，需要先update，修改文件，然后清除svn resolved，最后再提交commit)

## 查看文件详细信息

	svn info 文件名

## 查看日志

	svn log 文件名

## svn status [path]其字符的含义如下： 简写：svn st

<ul>
	<li>'' no modifications. 没有改动</li>
	<li>'A' Added. 新增加的项目</li>
	<li>'C' Conflicted. 项目内容与更新得到的数据冲突了</li>
	<li>'D' Deleted. 要删除的项目</li>
	<li>'I' Ignored. 已忽略</li>
	<li>'M' Modified. 内容有改动</li>
	<li>'R' Replaced. 在svn仓库中是一个目录, 在本地是一个同名的文件. 反之亦然.</li>
	<li>'X' 项目未版本化, 与外部连接相关</li>
	<li>'?' 未版本化的文件</li>
	<li>'!' 项目丢失, 或者说目录不完整</li>
	<li>'~' 本地有一个未版本化的项目, 在svn仓库里有一个同名项目</li>
</ul>

## 比较差异 svn diff path 简写：svn di
例如：

	svn diff test.php
	svn diff -r m:n path ##对版本m和版本n比较差异

例如：

	svn diff -r 200:201 test.php

## 将两个版本之间的差异合并到当前文件

	svn merge -r m:n path

例如：

	svn merge -r 200:205 test.php ##将版本200与205之间的差异合并到当前文件，但是一般都会产生冲突，需要处理一下

## svnserve.conf:12: Option expected

因为subversion读取配置文件svnserve.conf时，无法识别有前置空格的配置文件 去掉这些行前的#时，也要顺手去掉前面的空格

