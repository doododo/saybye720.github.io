---
layout:     post
title:      重新阅读PHP手册（一）
description: 关于PHP运行环境的问题
category:   blog
---


## 为何选用FPM管理FastCGI

FPM用于替换php FastCGI的大部分附加功能，对于处理高并发非常有用。

- 支持平滑停止/启动PHP进程的高级管理功能（可以单独重启）

eg.

```shell
$ /etc/init.d/php-fpm start
$ /etc/init.d/php-fpm stop
$ /etc/init.d/php-fpm reload
```

- 可以工作于不同的uid/gid/chroot环境下，并监听不同的端口和使用不同的`php.ini`配置文件（可取代`safe_mode`的配置）
- `stdout`和`stderr`日志目录
- 发生意外时，能够重启并缓存`opcode`
- 文件上传优化支持
- 可以记录脚本运行异常缓慢
- 在请求完成后，可以继续在后台执行耗时的工作
- 动态/静态子进程产生
- 基于`PHP.ini`的配置文件



## 关于php-cgi，FastCGI和PHP-FPM之间的关系

- `CGI`：（`Common Gateway Interface`）是 `Web Server` 与 `Web Application `之间数据交换的一种协议。

    `CGI`可以用任何语言编写，只要这种语言具有标准输入，输出和环境变量。如`PHP`,`Perl`,`Python`等。

    每处理一个`Web`请求都必须重新解析`php.ini`、重新载入全部扩展并初始化全部数据结构。

- `FastCGI`:同`CGI`，也是一种通讯协议，效率在`CGI`上做了一些优化。
    
    `FastCGI`更像常驻型的`CGI`，只要激活后，不会每次都fork一次。其初始化时会启动多个`CGI`解释器进程来等待来自`Web Server`的连接。当客户端请求到达`Web Server`时，`FastCGI`进程管理器会选择并连到一个`CGI`解释器，`Web Server`将`CGI`的环境变量和标准输入发送到`FastCGI`子进程，子进程处理完毕后将标准输出和错误信息返回`Web Server`。当`FastCGI`子进程关闭连接时，请求处理完成，接着等待并处理来自`FPM`的下一个连接。此外它还支持分布式运算。

    `FastCGI`在进程中的应用程序，独立于`Web`服务器核心运行，相对于`API`更安全。
    
    `FastCGI`不依赖于任何Web服务器的内部架构，相对稳定。

    `FastCGI`因为是多进程，所以比`CGI`多线程消耗更多内存。

- `PHP-CGI`：是`PHP`官方对`Web server`提供的`CGI`协议的接口程序。

    `php-cgi`变更`php.ini`配置后需重启`php-cgi`才能让新的php-ini生效，不可以平滑重启。

    直接杀死`php-cgi`进程，`php`就不能运行了。

    `PHP-FPM`和`Spawn-FCGI`就没有这个问题，守护进程会平滑从新生成新的子进程。

- `PHP-FPM`：是一个`PHP` `FastCGI`管理器，是只用于`PHP`。现在`PHP`官方已经将其整合到`PHP`源码包，其可以更好的管理`PHP`进程，有效的控制内存和进程，还可以平滑重载`PHP`配置。

- `Spawn-FCGI`是一个通用的`FastCGI`管理服务器，它是`lighttpd`中的一部份，目前独立运行。
    
    相对于`FastCGI`，`Spawn-FCGI`控制的进程CPU下降的很快，但内存分配的比较不均匀，


## Web Server传递数据还可以用PHP Module加载方式

`Module`加载方式就是把`php`作为`Web Server`的子模块来运行。例如`Apache`，当请求访问`php`文件时，`Apache`就会通过`LoadModule`调用`php5_module`，然后`php5_module`,通过`sapi`来解析`php`代码。

这种模式将`php`模块安装到`Apache`中，所以每一次`Apache`结束请求，都会产生一条进程，这个进程就完整的包括`php`的各种运算计算等操作。

`Apache`每接收一个请求，都会产生一个进程来连接`php`通过`sapi`来完成请求。所以一旦并发数过多，服务器压力就很大。

把`mod_php`编进`Apache`时，出问题时很难定位是`php`的问题还是`Apache`的问题。
