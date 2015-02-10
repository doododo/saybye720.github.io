---
layout:		post
title:		Laravel4.2中使用laravel/homestead进行开发
description: Laravel努力为整个PHP开发过程提供令人愉快的开发体验，也包括开发者的本地开发环境。
category:	blog
---
Laravel Homestead是一个官方的、预封装的Vagrant“箱子”，它提供给你一个奇妙的开发环境而不需要你在本机上安装PHP、HHVM、web服务器和其它的服务器软件。不用再担心搞乱你的操作系统！Vagrant箱子是完全可支配的。如果出现故障，你可以在几分种内完成销毁和重建箱子！

Homestead能运行在所有的Windows、Mac或Linux系统上，它包含了Nginx、PHP 5.6、MySQL、Postgres、Redis、Memcached和你开发神奇的Laravel应用程序需要的所有其它软件。

首先说下laravel/homestead的安装吧，官方的安装文档可以查看[Laravel Homestead][1],安装官方的文档配置的时候，会有报错，如下：

	The box failed to unpackage properly. Please verify that the box
	file you're trying to add is not corrupted and try again. The
	output from attempting to unpackage (if any):

	x ./box-disk1.vmdk: truncated gzip input
	bsdtar: Error exit delayed from previous errors.

很明显不用VPN下载不下来，用VPN下起来很慢，我试了。经过各种搜索，终于找到了不用VPN的下载地址：[下载地址][2],下载完成后直接从本地安装箱子到vagrant中去。然后运行

	vagrant box add laravel/homestead /Users/bigface/Downloads/virtualbox.box
	//成功
	==> box: Successfully added box 'laravel/homestead' (v0) for 'virtualbox'!

直接拿个实例来说吧，创建一个vagrent的目录。/Users/bigface/vagrant/，然后创建一个ddapp的项目目录，/Users/bigface/vagrant/ddapp/。在vagrant文件夹下克隆Homestead项目到本地。

	git clone https://github.com/laravel/homestead.git Homestead

切换到Homestead目录，运行Homestead init。修改Homestead.yaml配置文件。

	vi ~/.homestead/Homestead.yaml
	//文件如下
	---
	ip: "192.168.10.10"
	memory: 2048
	cpus: 1

	authorize: ~/.ssh/id_rsa.pub

	keys:
	    - ~/.ssh/id_rsa

	folders:
	    - map: /Users/bigface/vagrant
	      to: /home/vagrant/Code

	sites:
	    - map: dd.app
	      to: /home/vagrant/Code/ddapp/public

	databases:
	    - homestead

	variables:
	    - key: APP_ENV

修改hosts文件，添加 192.168.10.10  dd.app

进入Homestead目录，运行 vagrant up .

	vagrant ssh
	cd ~
	ls Code
	//显示如下
	vagrant@homestead:~/Code$ ls
	ddapp  Homestead  laravel.com


添加额外站点，ssh链接到虚拟机，运行命令：
	
	serve domain.app /home/vagrant/Code/path/to/public/directory

快速进入虚拟机

	//编辑~/.bashrc 添加命令别名,source下即可生效
	alias vm="ssh vagrant@127.0.0.1 -p 2222"



如此简单的东西搞了好几天，软件的使用和数据库密码之类的参考官网文档吧。

如何删除站点？

serve 命令会创建一个 nginx 的 site, 做些链接, 最后重启 nginx 和 php-fpm.所以到`/etc/nginx/sites-available/` 下把对应的 site 删除即可


[1]: http://v4.golaravel.com/docs/4.2/homestead
[2]: https://vagrantcloud.com/laravel/homestead/
