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