---
layout:		post
title:		PHP5.5下使用Ecshop
category:	blog
description:	关于ecshop安装时，应用时各种报错解决；去版权解决[仅用于学习]
---

以前都是每次用的时候解决下，到下次用的时候代码改烂了，又重新下载重新处理bug，这次一次性弄好了存起来。

## 安装过程中的的问题：cls_image::gd_version()和不支持JPEG

	Strict Standards: Non-static method cls_image::gd_version() should not be called statically in install\includes\lib_installer.php on line 31

原因：找到install/includes/lib_installer.php中的第31行   return cls_image::gd_version();然后在找到include/cls_image.php中的678行，发现gd_version()方法未声明静态static，所以会出错。修改

修改：将function gd_version()改成static function gd_version()即可

## 检测环境的时候提示：是否支持 JPEG是不支持的

这个错误我二话没说直接重新编译了下PHP，发现jpeg有安装，尼玛坑死我了。

解决：查看ecshop源代码发现install/includes/lib_installer.php中第100行,JPEG写成了JPG，正确的应该是：

	$jpeg_enabled = ($gd_info['JPEG Support']        === true) ? $_LANG['support'] : $_LANG['not_support'];

## preg_replace()报错

	Deprecated: preg_replace(): The /e modifier is deprecated, use preg_replace_callback instead in /Users/bigface/site/shop/includes/cls_template.php on line 1055

原因：PHP5.5不支持preg_replace函数，用preg_replace_callback函数替代

修改：修改cls_template.php  288 行 为

	return preg_replace_callback("/{([^\}\{\n]*)}/", function($r) { return $this->select($r[1]); }, $source);

cls_template.php页面里面有好几处这种错误，如下修改

	//$out = "<?php \n" . '$k = ' . preg_replace("/(\'\\$[^,]+)/e" , "stripslashes(trim('\\1','\''));", var_export($t, true)) . ";\n";
            $out = "<?php \n" . '$k = ' . preg_replace_callback("/(\'\\$[^,]+)/" , function($r) { return stripslashes(trim($r[1],'\''));}, var_export($t, true)) . ";\n";
            //+++
            //$val = preg_replace_callback("/\[([^\[\]]*)\]/eis", "'.'.str_replace('$','\$','\\1')", $val);
            $val = preg_replace_callback("/\[([^\[\]]*)\]/is", function($r){ return str_replace('$','\$',$r);}, $val);
            //+++
            $pattern     = '/<!--\s#BeginLibraryItem\s\"\/(.*?)\"\s-->.*?<!--\s#EndLibraryItem\s-->/s';
	//$replacement = "'{include file='.strtolower('\\1'). '}'";
            $replacement = function ($r) { return '{include file='.strtolower($r[1]). '}';};
            $source      = preg_replace_callback($pattern, $replacement, $source);
            //+++




## array_shift(explode(' ', $tag))函数的参数是引用传递的，5.3以上默认只能传递具体的变量，而不能通过函数返回值

	Strict standards: Only variables should be passed by reference in /Users/bigface/site/shop/includes/cls_template.php on line 407

修改：

	$rs = explode(' ', $tag);
            $tag_sel = array_shift($rs);

## mktime()方法不带参数被调用时，会被抛出一个报错提示

	Strict standards: mktime(): You should be using the time() function instead in /Users/bigface/site/shop/admin/sms_url.php on line 31

修改：改为

	$auth = time();

## 去除版权

1.去掉头部TITLE部分的ECSHOP演示站直接在后台商店设置 – 商店标题修改

2.去除Powered by ecshop，打开includes/lib_main.php，156行，去除' – ' . 'Powered by ECShop';

3.去友情链接里的，直接在后台友情链接里面删除

4.去掉底部的Powered by Ecshop  v 2.7.3。打开 js/common.js   删除第261行:onload = function(){所有代码}，打开模板文件夹的 library/page_footer.lbi 删除 {foreach from=$lang.p_y item=pv}{$pv}{/foreach}{$licensed}

5.去除后台登陆时的ecshop图标            admin/images/ecshop_logo.gif

6.登录成功后左上角的ecshop图标；    admin/images/login.png

7.后台成功登录后，右上角的“关于ECSHOP”打开admin/templates/top.htm删除：

	<li><a href="index.php?act=about_us" target="main-frame">{$lang.about}</a></li>

8.同理去除后面紧挨着的帮助

9.中部 ECSHOP-管理中心， 和底部的版权所有，底部的“版权所有 2005-2011 上海商派网络科技有限公司，并保留所有权利。”都在language/zh_cn/admin/common.php里面，自己看着改吧

10.修改 languages\zh_cn\admin\index.php 中 删除相关的

11.修改 includes\cls_ecshop.php.php     大概 21行 起

	define(‘APPNAME’, ‘ECSHOP’);
	define(‘VERSION’, ‘v2.7.1′);
	define(‘RELEASE’, ’20091228′);

12.修改后台提醒最新版本信息
搜索，elseif ($_REQUEST['act'] == 'main_api')，直接去除此函数

