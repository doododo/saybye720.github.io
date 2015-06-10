---
layout:		post
title:		Redis使用与实践
description: Redis是Key-Value类型的内存数据库，可以是String,List,Set,Sorted Set,Hash等
category:	blog
---

##windows下phpredis的安装

-下载 redis 服务端

[windows phpredis 服务端][1] 下载

-下载redis php扩展，很多好心人直接编译好的.dll文件 

[php redis 扩展][2] 下载

-将扩展加入php.ini 

	[redis]
	extension = php_igbinary.dll
	extension = php_redis.dll

-查看phpinfo是否添加成功

-启动redis服务端。直接点击start.bat运行就可以。不能关闭窗口，不然redis服务也关闭。

-测试

	<?php
		$redis = new Redis();
		$redis->connect("127.0.0.1","6379");  //php客户端设置的ip及端口
		//存储一个 值
		$redis->set("say","Redis test!");
		echo $redis->get("say");
	?>

##Redis相关命令

-运行Redis服务器 `/usr/bin/redis-server /etc/redis.conf`

-查看是否启动 `ps -x |grep redis`

##Redis支持类型总结

-String：可以做统计计算，如网站访问次数

-List: 可以实现消息列队，减少数据库压力。如用rpush命令把消息放入队列尾部，lpop命令把消息从队列头部取出。

-Set: 常用于记录做过某些事情。如投票系统，每个用户每天只能投一票，只需要将日期作为Set的key，用户ID作为member即可。要查看用户今天是否投过票，只需要将今天日期作为key去查询此用户ID是否存在。

-Sorted Set：常用于做一些排行榜。如排行榜中按点击次数排序。将排序值设置成Sorted Set的score值，将具体数据设置成相应的value，用户每次点击时，只需要执行zadd命令修改score值

-Hash：适合用于存储对象，比如，验证码，用户session信息等


[1]: http://pan.baidu.com/s/1pJC6meR "windows phpredis server"
[2]: http://pan.baidu.com/s/1kT7oCy7 "phpredis扩展"