---
layout:     post
title:      Laravel5.1错误记录
category: blog
description: 把每次做项目遇到的问题记录下，以便下次及时解决问题
---

##Composer安装组件报错

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
