---
layout:		post
title:		Laravel 4.*常见报错及解决办法-大脸整理
description: Laravel是一套功能强大的PHP开发框架，并且着重于灵活性和语法的表现力。Laravel的灵活性能让你随心所欲的更新、重构你用应用；Laravel富于表现力的语法能让你或你的团队的代码简洁、容易阅读。
category:	blog
---

## 手动添加类或者方法找不到

	composer dump-update

##Class 'Monolog\Logger' not found

I was able to resolve this issue by changing my "minimum-stability" from "dev" to "stable" in composer.json. Then running composer update.
老外原话，直接按照修改composer.json文件即可

## 生成加密key

	php artisan key:generate

##Laravel4.2中Redis报错

在Laravel4.2中关于session存储到redis中，报错
	
	Call to undefined method Redis::connection()

报错原因：phpredis扩展中的类也叫Redis，与laravel中封装的redis api类名相同，改下app/config/app.php中alais里的

	'Redis'           => 'Illuminate\Support\Facades\Redis',
	//改为
	'LRedis'           => 'Illuminate\Support\Facades\Redis',

没有通过PECL为PHP安装redis扩展模块，则无需修改


