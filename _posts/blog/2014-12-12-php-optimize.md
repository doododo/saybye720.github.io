---
layout:		post
title:		PHP性能优化问题
category:	blog
description:	PHP性能优化主要分为三个部分，优化代码-优化逻辑-优化语言
---

##apache bench压力测试工具

	ab --help  //查看命令帮助
	ab -n1000 -c100 http://www.baidu.com/   //-n请求数   -c并发数   测试目标，不要忘记  /结尾

1.多用内置函数，少用魔法函数

2.在开发过程中尽量不要@错误抑制符

3.尽量不要在字符串中进行运算
	
	for($i=0;$i<srtlen($srt);$i++){
		//srtlen()函数会产生很多内存开销
	}

##Gzip压缩适用于较大网页

##PHP性能分析工具XHPorf
这是一款院子facebook的性能分析工具。
	
	php --ri xhprof //检查是否支持此扩展

##vld分析opcode代码的PHP扩展

##PHP终极优化思路

1.Opcode Cache：可以用APC扩展，去[pecl官方网站][]。但此扩展2012年就停止更新了，关于Opcode缓存可以看下鸟哥的yac。

2.Runtime:HHVM,facebook出的。



[pecl官方网站]: "http://pecl.php.net/" "PHP扩展官方网站" 
