---
layout:		post
title:		OneThink内测版发送邮件问题
description: OneThink是一个开源的内容管理框架，基于最新的ThinkPHP3.2版本开发，提供更方便、更安全的WEB应用开发体验，采用了全新的架构设计和命名空间机制，融合了模块化、驱动化和插件化的设计理念于一体，开启了国内WEB应用傻瓜式开发的新潮流。
category:	blog
---

## 介绍
关于OneThink 的介绍就不多说了，想要了解的朋友就去看[OneThink][1]手册吧

## 发送邮件
今天onethink总是报错："Class 'PHPMailer' not found"，我看了官网的教程，还有很多百度的教程，我发现哪里都没有错。先把官网的教程链接发下
[http://www.thinkphp.cn/code/32.html][2]

ThinkPHP的核心文件就不看了，有空的自己看下，直接看OneThink是一个开源的内容管理框架，基于最新的ThinkPHP3里的3.2框架吧。
ThinkPHP/ThinkPHP.php里的系统常量定义

	defined('LIB_PATH')     or define('LIB_PATH',       THINK_PATH.'Library/'); // 系统核心类库目录
	defined('VENDOR_PATH')  or define('VENDOR_PATH',    LIB_PATH.'Vendor/'); // 第三方类库目录

再看ThinkPHP/Common/functions.php里

	function vendor($class, $baseUrl = '', $ext='.php') {
	    if (empty($baseUrl))
	        $baseUrl = VENDOR_PATH;
	    return import($class, $baseUrl, $ext);
	}

这个函数，就知道了，vendor 默认加载的文件夹不是在ThinkPHP/Extend  而是在ThinkPHP/Library/Vendor
OK 把PHPMailer文件夹自己移动过去就OK了。

某些人可能高兴的比较早。如果出现这个错误“Could not connect to SMTP host”

打开class.PHPMailer.php找到

	public function IsSMTP() {
	    $this->Mailer = 'SMTP';
	  }



[1]: http://document.onethink.cn/manual_1_0.html "OneThink手册"
[2]: http://www.thinkphp.cn/code/32.html