---
layout:		post
title:		Mac OS X 如何安装redis，并在PHP中使用redis
category:	blog
description:	最近打算研究一下redis，所以试试在MacOS下安装redis服务，看来并没有想象中那么困难。

---

##下载最新版的redis

[redis官网][1]

##安装和编译
	
	make && make install

make命令执行完成后，会在当前目录下生成本个可执行文件，分别是redis-server、redis-cli、redis-benchmark、redis-stat，它们的作用如下：

redis-server：Redis服务器的daemon启动程序
redis-cli：Redis命令行操作工具。当然，你也可以用telnet根据其纯文本协议来操作
redis-benchmark：Redis性能测试工具，测试Redis在你的系统及你的配置下的读写性能
redis-stat：Redis状态检测工具，可以检测Redis当前状态参数及延迟状况

##编译之后的文件路径

	mkdir -p /usr/local/bin
	cp -pf redis-server /usr/local/bin
	cp -pf redis-benchmark /usr/local/bin
	cp -pf redis-cli /usr/local/bin
	cp -pf redis-check-dump /usr/local/bin
	cp -pf redis-check-aof /usr/local/bin

##修改配置文件

先把配置文件拷贝到/etc目录

	cp -pf redis.conf /etc目录

在安装完成之后，要启动，需要配置文件，在下载的文件加下面有一个默认的配置文件，可以修改参数

在我们成功安装Redis后，我们直接执行redis-server即可运行Redis，此时它是按照默认配置来运行的（默认配置甚至不是后台运行）。我们希望Redis按我们的要求运行，则我们需要修改配置文件，目前它被我们拷贝到了/etc目录下。修改它就可以配置我们的server了。如何修改？下面是redis.conf的主要配置参数的意义：

daemonize：是否以后台daemon方式运行
pidfile：pid文件位置
port：监听的端口号
timeout：请求超时时间
loglevel：log信息级别
logfile：log文件位置
databases：开启数据库的数量
save * *：保存快照的频率，第一个*表示多长时间，第三个*表示执行多少次写操作。在一定时间内执行一定数量的写操作时，自动保存快照。可设置多个条件。
rdbcompression：是否使用压缩
dbfilename：数据快照文件名（只是文件名，不包括目录）
dir：数据快照的保存目录（这个是目录）
appendonly：是否开启appendonlylog，开启的话每次写操作会记一条log，这会提高数据抗风险能力，但影响效率。
appendfsync：appendonlylog如何同步到磁盘（三个选项，分别是每次写都强制调用fsync、每秒启用一次fsync、不调用fsync等待系统自己同步）

##启动服务器

	./redis-server 

或 

	$redis-server /etc/redis.conf  

查看是否成功启动 

	$ ps -ef | grep redis   

##验证

	./redis-cli ping 
	PONG

##在PHP中使用redis

###下载phpredis

[下载地址][2]

###编译安装

	phpize
	./configure [--enable-redis-igbinary]
	make && make install

如果phpize报错，参考[系统升级到后安装php扩展问题][3]，注意，如果一定要注意自己Xcode的位置

###配置php.ini

安装完成后redis.so自动拷贝到PHP扩展文件夹，在php.ini添加扩展即可，然后重启apache

###测试
	
	php -m | grep redis
	显示redis

##以上扩展可以用Homebrew来安装

关于homebrew安装参考[Mac OS X 不可或缺的套件管理器 -- Homebrew][4]
关于homebrew使用参考[Mac OS X 下 Homebrew 的使用][5]

[1]: http://www.redis.io/download
[2]: https://github.com/nicolasff/phpredis
[3]:http://blog.11010.net/macos-apache-php-ask/
[4]: http://blog.11010.net/mac-homebrew/
[5]: http://blog.11010.net/mac-homebrew-used/