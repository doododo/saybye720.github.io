---
layout:		post
title:		Yosemite下配置PHP支持GD库FreeType
category:	blog
description:	Mac升级系统到 Yosemite 10.10，偶然发现验证码不显示了，php -m 发现有GD库，但没有发现FreeType。

---
Mac升级系统到 Yosemite 10.10，偶然发现验证码不显示了，php -m 发现有GD库，但没有发现FreeType。

##安装 FreeType

前往苹果官方开源支持：http://www.apple.com/opensource/ 查找并下载GD需要的 zlib/libpng/jpeg/freetype/libgd，这里提供一个包及执行脚本：

[百度网盘][1]

解压，安装
	
	cd gd
	sudo ./install

此时，已经安装完freetype，但需要重新编译PHP，添加 FreeType 支持，否则在PHP上仍然不能获得Freetype支持……

##重新编译PHP

查看自己PHP版本，Yosemite应该是PHP5.5.14，下载地址[百度网盘][2]

下载解压后移动到/usr/local/php  并进行编译

	./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/lib/php/ --with-apxs2=/usr/sbin/apxs --with-mysql --with-mysqli=/usr/local/mysql/bin/mysql_config --with-zlib-dir=/usr/local/opt/zlib --with-curl --with-openssl --enable-mbstring --enable-sockets --enable-pdo --with-pdo-mysql --enable-xml --enable-zip --with-mcrypt=/usr/local/mcrypt --with-gd --with-jpeg-dir=/usr/local/opt/jpeg --with-png-dir --with-freetype-dir=/usr/local/opt/freetype

	make && make install

出现一下错误，表示编译路径不对

	configure: WARNING: unrecognized options: --with-config-file-path, --with-apxs2, --with-mysql, --with-zlib, --enable-mbstring, --enable-xml, --with-gd, --with-jpeg-dir, --with-png-dir, --with-freetype-dir
	./configure: line 2481: syntax error near unexpected token `config.nice'
	./configure: line 2481: `PHP_CONFIG_NICE(config.nice)'

出现以下错误
	./configure: No such file or directory

一般情况下，多看看目录下的readme和INSTALL文件，里面会告诉你怎么安装软件。
目录下没有configure，但有configure.am或configure.in时，需要用autoconf命令来生成configure。代码如下：

	$cd (软件名)-(版本号)
	$autoconf

安装编译完后提示

	You may want to add: /usr/local/php/lib/php to your php.ini include_path

安装提示拷贝过去

发现phpinfo里面 输出 loaded configuration file none

直接打开Apache配置文件把，在文件开头加入

	PHPIniDir "/usr/local/php/lib/php/"

重启Apache

[1]: http://pan.baidu.com/s/1gdgmVuz
[2]: http://pan.baidu.com/s/1hq9rhMO
