---
layout:		post
title:		阿里云安装vsftp及注意事项
description: 阿里云服务器配置完了，总感觉还少点什么，得有个ftp服务。开始吧，CentOS Linux Vsftp服务器配置 
category:	blog
---

## 查看是否已经安装vsftpd

	rpm -qa | grep vsftpd   

## yum安装vsftpd

	yum install vsftpd -y

## 设置每次开机时自动运行及手工启动它

	chkconfig vsftpd on    
	service vsftpd start     
	netstat -tl   
	##最后这条命令可以查看ftp端口是否在侦听了

## 配置vsftp
编辑vsftp配置文件，命令如下：

	#vi /etc/vsftpd/vsftpd.conf

将配置文件中”anonymous_enable=YES “改为 “anonymous_enable=NO”
取消如下配置前的注释符号：

	local_enable=YES
	write_enable=YES
	chroot_local_user=YES

保存修改，按ESC键，输入：wq

启动vsftp服务并测试登录
使用命令启动vsftp服务：

	#service vsftpd start

然后用帐号pwftp测试下是否可以登陆ftp。目录是/alidata/www/wwwroot

## 添加用户

先检查一下nologin的位置，通常在/usr/sbin/nologin或者/sbin/nologin下。
	#useradd -s /sbin/nologin -d /alidata/www/wwwroot -g ftp cha
	注：如果出现：useradd：警告：此主目录已经存在。
            不从 skel 目录里向其中复制任何文件。

## 删除用户
	
	#userdel -r cha

## 修改该帐户密码:

	#passwd cha

## 参考
[CentOS 5.5下安装vsftpd服务器][1]

[1]: http://www.codesky.net/article/201103/174648.html