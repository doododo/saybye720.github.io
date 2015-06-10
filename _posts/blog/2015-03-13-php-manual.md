---
layout:		post
title:		PHP手册随手记
description: 从事PHP研发5年了，反而PHP基础的东西忘了很多，开始查看PHP和MySQL手册，特此记录。万变不离其宗。想要突破瓶颈，还得回头看下基础手册。
category:	blog
---

##类型

>在单引号字符串中变量和特殊字符的转义序列不会被替换

eg：
	
	<?php
		echo 'This is a apple \n That is bananer.';
		// 输出 This is a apple \n That is bananer.

>`foreach循环中$value仅在被遍历数组可引用时才可用（例如是个变量）`

>foreach循环中$value引用在循环结束后仍会保留，unset($value)将其销毁

>`foreach`循环中不可用@屏蔽错误