---
layout:		post
title:		阿里云lnmp配置及报错处理
description: 基本的安装去阿里云论坛一搜一大堆，而且都是版本比较新的，就不多说了，主要记录下本人在安装过程中遇到的问题吧
category:	blog
---

## LNMP安装完成后502报错
先重启，重启后发现502都没有了，IP直接无法访问。后来fdisk -l查看服务器，原来数据盘没有挂载。
挂载数据盘：

	mount /dev/xvdb1 /alidata/

挂载完后肯定是重启服务了

## 重启vsftpd 

	service vsftpd start

发现失败，淡定，运行如下：

	service vsftpd stop 关闭下，然后运行重启，OK。

重启mysql

	/etc/rc.d/init.d/mysqld start

重启

	/alidata/server/nginx/sbin/nginx -s reload

发现错误又来了

	nginx: [error] open() "/alidata/server/nginx/logs/nginx.pid" failed (2: No such file or directory)

先查看下是什么原因

	ls /alidata/server/nginx/logs/

发现没有nginx.pid


运行如下（使用nginx -c的参数指定nginx.conf文件的位置）

	/alidata/server/nginx/sbin/nginx -c /alidata/server/nginx/conf/nginx.conf

然后运行：


	/alidata/server/nginx/sbin/nginx -s reload

OK没有报错了，再运行

	ls /alidata/server/nginx/logs/ 

发现多了个nginx.pid

	/alidata/server/nginx/sbin/nginx -s reload

==============华丽的分割线====================

大功告成，按照官网的访问下IP结果出现502.我日。安装的时候我默认的选择的PHP5.3。
PHP5.3不支持Zend Optimizer,找出PHP.ini直接翻到末尾，注释掉所有的Zend  Optimizer 
然后重启PHP-fpm

	sbin/php-fpm 

	sbin/php-fpm -c /alidata/server/php/etc/php.ini -y /alidata/server/php/etc/php-fpm.conf

发现报错

	ERROR: unable to bind listening socket for address '127.0.0.1:9000': Address already in use (98)

找到错误就好说了

	netstat -ntlp|grep 9000
	killall php-fpm
	sbin/php-fpm -c /alidata/server/php/etc/php.ini -y /alidata/server/php/etc/php-fpm.conf

OK，访问IP毫无疑问显示了phpwind的安装提示，根据自己的需求配置nginx吧

## 阿里云Nginx配置虚拟主机

1.新建文件夹(存放源码)

	touch /alidata/www/bigface

2.新建虚拟主机文件

	touch /alidata/server/nginx/conf/vhosts/bigface.conf

3.新建伪静态文件

	touch /alidata/server/nginx/conf/rewrite/bigface.conf

4.新建日志文件

	touch /alidata/log/nginx/access/bigface.log

OK！
下面开始测试
	
	/alidata/server/nginx/sbin/nginx -t

如果显示：

	nginx: the configuration file /alidata/server/nginx/conf/nginx.conf syntax is ok
	nginx: configuration file /alidata/server/nginx/conf/nginx.conf test is successful

如果出现以上两句话就说明配置成功了。

下面重启下nginx

	/alidata/server/nginx/sbin/nginx -s reload

Ok！

## Zend Optimizer不支持php5.3

关于阿里云一键安装web服务器的疑问

如果修改sh安装包里面的install.sh文件，默认安装php5.3的话，ZendOptimizer也给安装上，5.3以上不支持ZendOptimizer,肯定会出错。删除php.ini里的ZendOptimizer的相关信息换成ZendGuardLoader，各种报错，最后发现`Thread Safety enabled`  。浪费了这么长时间

## Nginx禁止IP访问

直接在conf文件底部添加这段代码

	server {
	    listen 80 default;
	    return 500;
	}

## Nginx域名跳转加www

eg.nginx如何设置11010.net 跳转www.11010.net

	if ($host = '11010.net' ) {
	    rewrite ^/(.*)$ http://www.11010.net/$1 permanent;
	} 


