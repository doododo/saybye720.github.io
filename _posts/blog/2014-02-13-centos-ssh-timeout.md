---
layout:		post
title:		Centos下设置ssh连接时间
description: Centos连接时间太短，经常出现安装某个软件的时候SSH断开了，其实可以让程序运行的时候挂起，每次这样太费劲了，直接修改SSH连接时间
category:	blog
---

基于安全的理由，如果用户连线到 SSH Server 后闲置，SSH Server 会在超过特定时间后自动终止 SSH 连线。以下是设定终止连线时间的方法: 

1、开启 `/etc/ssh/sshd_config` 档案，里面有一个参数为 ClientAliveCountMax，它是设定用户端的 SSH 连线闲置多长时间后自动终止连线的数值，单位为分钟。 

2、如果这一行最前面有#号,将那个#号删除，并修改想要的时间。 

3、修改后储存及关闭档案，重新启动 sshd: 

	`/etc/rc.d/init.d/sshd restart`

(必须是绝对路径)