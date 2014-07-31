---
layout:     post
title:      阿里云云服务器硬盘分区及挂载
category: blog
description: 阿里云服务器硬盘挂载及分区
---

云服务器环境：aliyunlinux

客户端环境：Mac OSX

远程连接方式：运行 Terminal，输入命令 ssh username@ip

硬盘分区及挂载操作步骤：

1. 查看未挂载的硬盘（名称为/dev/xvdb）

	# fdisk -l 

	Disk /dev/xvdb doesn't contain a valid partition table

2. 创建分区

	# fdisk /dev/xvdb

输入n

	Command (m for help):n

输入p

	Command action
	e extended
	p primary partition (1-4)
	p

输入1

	Partition number (1-4): 1

回车

	First cylinder (1-2610, default 1): 
	Using default value 1

回车

	Last cylinder, +cylinders or +size{K,M,G} (1-2610, default 2610): 
	Using default value 2610

输入w

	Command (m for help): w
	The partition table has been altered!

3. 格式化分区

	# mkfs.ext3 /dev/xvdb1

4. 建立挂载目录

	# mkdir /data

5. 挂载分区

	# mount /dev/xvdb1 /data

6. 设置开机自动挂载

	vi /etc/fstab

在vi中输入i进入INERT模式，将光标移至文件结尾处并回车，将下面的内容复制/粘贴，然后按Esc键，输入:x保存并退出

	/dev/xvdb1              /data                   ext3    defaults        0 0

7. 确认是否挂载成功

重启服务器

	# reboot

查看硬盘分区

	# df -h

	/dev/xvdb1            20635700    176196  19411268   1% /data

搞定！不过还是感觉以后别这么玩了，直接一件安装包吧
参考地址[阿里云一键安装][1]

[1]: http://help.aliyun.com/view/13435438.html 