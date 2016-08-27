---
layout:     post
title:      Laravel5.1错误记录
description: 把每次做项目遇到的问题记录下，以便下次及时解决问题
category: blog
---

## Composer安装组件报错

问题如下：

    composer require 'toplan/laravel-sms:~2.3.1'
    ./composer.json has been updated
    > php artisan clear-compiled
    Loading composer repositories with package information
    Updating dependencies (including require-dev)
    Failed to decode response: zlib_decode(): data error
    Retrying with degraded mode, check https://getcomposer.org/doc/articles/troubleshooting.md#degraded-mode for more info

    Installation failed, reverting ./composer.json to its original content.

    [Composer\Downloader\TransportException]
    Your configuration does not allow connections to http://87.98.253.214/p/jeremeamia/superclosure%2479719c27a2bdcb1110375e344fecda9ee0a8a79c4ce99c35c2bfa1a3b48907e1.json. See https://getcomposer.org/doc/06-config.md#secure-http for details.

    require [--dev] [--prefer-source] [--prefer-dist] [--no-plugins] [--no-progress] [--no-update] [--update-no-dev] [--update-with-dependencies] [--ignore-platform-reqs] [--sort-packages] [-o|--optimize-autoloader] [-a|--classmap-authoritative] [--] [<packages>]...

解决问题：composer只允许通过HTTPS下载

    composer config --global repositories.packagist.allow_ssl_downgrade false


## Mac安装composer

* 官网下载 [https://getcomposer.org/download/][1]，

* 打开终端

```
sudo mv composer.phar /usr/local/bin/composer
```

当我们运行`composer -v` 的时候发现没有权限

```
bigface:~ bigface$ composer
-bash: /usr/local/bin/composer: Permission denied
```

* 修改权限

```
chmod +x /usr/local/bin/composer
```

## 安装homestead报错

```
[bigface:~ bigface$ vagrant box add laravel/homestead
==> box: Loading metadata for box 'laravel/homestead'
    box: URL: https://atlas.hashicorp.com/laravel/homestead
==> box: Adding box 'laravel/homestead' (v0.5.0) for provider: virtualbox
    box: Downloading: https://atlas.hashicorp.com/laravel/boxes/homestead/versions/0.5.0/providers/virtualbox.box
An error occurred while downloading the remote file. The error
message, if any, is reproduced below. Please fix this error and try
again.

SSL read: error:00000000:lib(0):func(0):reason(0), errno 60
```

解决方案：

```
rm -rf ~/.vagrant.d/tmp/*
```

## ErrorException in Filesystem.php line 81

问题：[http://laravel.io/forum/09-02-2015-errorexception-in-filesystemphp-line-81][2]

解决方案：

```
mkdir -p storage/framework/views
```

[1]: https://getcomposer.org/download/ "Composer下载"
[2]: http://laravel.io/forum/09-02-2015-errorexception-in-filesystemphp-line-81 "larave官方论坛问题描述"
