---
layout:		post
title:		Mac OS X PHP开发常见的问题及处理
description: Mac OS X 做开发很方便，自带Apache,PHP,Java,Python,perl,Ruby等
category:	blog
---

## 解决apache启动错误

	httpd: Could not reliably determine the server's fully qualified domain name, using bigfacetekiMacBook-Air.local for ServerName

解决方法：
1.进入apache的安装目录：(视个人安装情况而不同)

	root# vim /etc/apache2/httpd.conf

2.编辑httpd.conf文件，搜索"#ServerName"，添加ServerName localhost:80

	vi httpd.conf
	#ServerName www.example.com:80
	ServerName localhost:80

3.测试apache配置文件修改的是否正确

	apachectl configtest

4.再重新启动apache 即可

	root# apachectl restart

## 解决apache日志文件太大的方法

1.找到apache 配置http.conf

	vim /etc/apache2/httpd.conf

2.注释

	#ErrorLog "/private/var/log/apache2/error_log"

改为

	# 限制错误日志文件为 1M
	ErrorLog "| /usr/sbin/rotatelogs /private/var/log/apache2/%Y-%m-%d_error_log 86400 480"

注释

	#CustomLog "/private/var/log/apache2/access_log" common

改为

	CustomLog "| /usr/sbin/rotatelogs /private/var/log/apache2/%Y-%m-%d_access_log 86400 480" common

3.重启apache

	apachectl restart

4.查看目录

	ls /var/log/apache2/
	2014-03-24_access_log 2014-03-24_error_log

注：accss_log 如果没有出现不要着急，访问下网站即可

## 系统升级到后安装php扩展问题

在装mcrypt插件时报错，提示：

	mcrypt fatal error: 'php.h' file not found

然后又仔细操作了一次在输完phpize回车时就已经开始出错了，出错信息如下:

	grep: /usr/include/php/main/php.h: No such file or directory
	grep: /usr/include/php/Zend/zend_modules.h: No such file or directory
	grep: /usr/include/php/Zend/zend_extensions.h: No such file or directory
	Configuring for:
	PHP Api Version:
	Zend Module Api No:
	Zend Extension Api No:

解决办法：

	sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include /usr/include

开始安装各种插件，直接上代码

1.安装autoconf插件

	cd ..
	curl -O http://mirrors.kernel.org/gnu/autoconf/autoconf-2.65.tar.gz
	tar -xzvf autoconf-2.65.tar.gz
	cd autoconf-2.65
	./configure –prefix=/usr/local
	make
	sudo make install

2.安装libmcrypt插件

	tar zxvf libmcrypt-2.5.8.tar.gz
	cd libmcrypt-2.5.8/
	./configure --disable-posix-threads --enable-static
	make
	sudo make install

3.下载并解压PHP源码文件php-5.3.4.tar.gz。Mac OS X 10.6.3中预装的PHP版本是5.3.4，所以需要下载这个版本
在终端执行如下命令：

	tar zxvf php-5.3.4.tar.gz
	cd php-5.3.4/ext/mcrypt
	phpize
	./configure
	make
	sudo cp modules/mcrypt.so /usr/lib/php/extensions/no-debug-non-zts-20090626/

4.打开php.ini

	sudo vi /etc/php.ini

5.在php.ini中加入如下代码，并保存后退出，然后重启Apache

	extension=/usr/lib/php/extensions/no-debug-non-zts-20090626/mcrypt.so

+++++++++++++++++++++++++++++++++++++++++
+ 注：mac os x 10.9.2                    +
+ apache 扩展目录在 /usr/libexec/apache2/ +
+ php 扩展目录 /usr/lib/php/extensions/   +
+++++++++++++++++++++++++++++++++++++++++
