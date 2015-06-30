---
layout:     post
title:      阿里云一键安装包如何安装fileinfo扩展
category: blog
description: 本模块中的函数通过在文件的给定位置查找特定的 魔术 字节序列 来猜测文件的内容类型以及编码。 虽然不是百分百的精确， 但是通常情况下能够很好的工作。
---

今天把laravel5.0的一个向项目部署到阿里云上测试，'composer update'的时候报错
	
	Problem 1
    - intervention/image 2.3.0 requires ext-fileinfo * -> the requested PHP extension fileinfo is missing from your system.
    - intervention/image 2.2.2 requires ext-fileinfo * -> the requested PHP extension fileinfo is missing from your system.
    - intervention/image 2.2.1 requires ext-fileinfo * -> the requested PHP extension fileinfo is missing from your system.
    - intervention/image 2.2.0 requires ext-fileinfo * -> the requested PHP extension fileinfo is missing from your system.
    - Installation request for intervention/image ^2.2 -> satisfiable by intervention/image[2.2.0, 2.2.1, 2.2.2, 2.3.0].

缺少扩展fileinfo，安装fileinfo扩展，从自带的一键安装包里面进入php扩展目录编译、安装

	cd sh-1.4.1/php-5.5.7/ext/fileinfo/
	phpize

		Configuring for:
		PHP Api Version:         20121113
		Zend Module Api No:      20121212
		Zend Extension Api No:   220121212

	./configure --disable-fileinfo
	make && make install

	Installing shared extensions:     /alidata/server/php/lib/php/extensions/no-debug-non-zts-20121212/   //安装完成

查看了下扩展目录已经有了fileinfo.so，然后修改php.ini，添加fileinfo.so，重启nginx

	/alidata/server/nginx/sbin/nginx -s reload

终于可以更新项目扩展包了。

	php composer.phar update

错误又来了。

	The authenticity of host 'github.com (192.30.252.131)' can't be established.
	RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
	Are you sure you want to continue connecting (yes/no)? yes


                                                                                                                                                  
	[RuntimeException]                                                                                                                              
	Failed to clone git@github.com:sebastianbergmann/php-code-coverage.git via git, https, ssh protocols, aborting.                                 
	- git://github.com/sebastianbergmann/php-code-coverage.git                                                                                      
	fatal: Unable to look up github.com (port 9418) (Name or service not known)                                                                   
	- https://github.com/sebastianbergmann/php-code-coverage.git                                                                                    
	fatal: unable to access 'https://github.com/sebastianbergmann/php-code-coverage.git/': Could not resolve host: github.com; Name or service n  
	ot known                                                                                                                                        
	- git@github.com:sebastianbergmann/php-code-coverage.git                                                                                        
	Warning: Permanently added 'github.com,192.30.252.131' (RSA) to the list of known hosts.                                                      
	Permission denied (publickey).                                                                                                                
	fatal: Could not read from remote repository.                                                                                                 
	                                                                                                                                              
	Please make sure you have the correct access rights                                                                                           
	and the repository exists.                                                                                                                    
                                                                                                                                                  


	update [--prefer-source] [--prefer-dist] [--dry-run] [--dev] [--no-dev] [--lock] [--no-plugins] [--no-custom-installers] [--no-autoloader] [--no-scripts] [--no-progress] [--with-dependencies] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [--ignore-platform-reqs] [--prefer-stable] [--prefer-lowest] [packages1] ... [packagesN]

github给限制了，需要tocken验证。我擦了。。更新的时候输入tocken才可以下载。。。根据提示输入网址，然后获取tocken，复制，粘贴到输入框即可

	https://github.com/settings/tokens/new?scopes=repo&description=Composer+on+iZ25ac1se6aZ+2015-06-30+1644

	获取到

	8e5b1ae405f7f912731fced1d2f7e603c437cb34

OK。继续干活。

