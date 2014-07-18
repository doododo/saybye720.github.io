---
layout:		post
title:		Laravel4如何添加包依赖
description: 所有的插件无非都是这个流程，其实在搜索插件的时候都有安装方法，有的插件安装完需要配置
category:	blog
---

1.编辑composer.json文件 把需要的包依赖写入
	
	"require-dev": {
	    "way/generators": "2.*"
	}

2.在命令行里运行
	
	composer update --dev

3.打开app/config/app.php文件，添加到providers数组

	'Way\Generators\GeneratorsServiceProvider'

4.项目根目录运行

	php artisan

