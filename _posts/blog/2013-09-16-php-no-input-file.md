---
layout:		post
title:		运行PHP出现No input file specified错误的解决办法
description: 阿里云Windows服务器 运行PHP出现No input file specified错误
category:	blog
---

今天配置了一台新服务器，使用的是`IIS + Fastcgi + PHP 5.3.X`，在默认网站下运行都是正常的，但是新增一台虚拟主机或网站访问php页面的时候就会报错`No input file specified`。按照网上的各种解决方法，如屏蔽doc_root、给目录加权限、修改缓冲池等等都无效。

后来发现一个解决的文档，找着试了确实管用，方法如下：

打开php.ini文件，这个你看你怎么配置的了。

在php.ini文件里面修改：

1、增加一行（这个最重要）

     fastcgi.impersonate = 1

2、修改两项（解开注释就可以了）

     cgi.fix_pathinfo=1
     cgi.force_redirect = 0