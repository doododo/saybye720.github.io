---
layout:		post
title:		wamp安装phpredis
description: windows下开发最大的弊端就是命令行太弱。
category:	blog
---

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

[1]: http://pan.baidu.com/s/1pJC6meR "windows phpredis server"
[2]: http://pan.baidu.com/s/1kT7oCy7 "phpredis扩展"