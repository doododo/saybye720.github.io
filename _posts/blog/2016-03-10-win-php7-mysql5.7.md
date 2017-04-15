---
layout:   post
title:    windows8.1下安装压缩包版PHP7.0和mysql5.7
description: win下的PHP开发环境无需再装wamp或者xamp，版本和扩展问题可能不太适合项目的开发，直接解压缩，简单粗暴
category: blog
---

## 准备

下载官网php7.0和mysql5.7

加压php7到E盘，重命名为PHP7

## 配置PHP.ini

复制php.ini-production 为 php.ini

修改php.ini

    ; On windows:
    extension_dir = "E:\PHP7\ext"
    //按照自己的需要开启服务，去掉前面的;号即可

    session.save_path = "/tmp"修改为
    session.save_path = "E:\phptmp\session"
    upload_tmp_dir =  修改为
    upload_tmp_dir = "E:\phptmp\upload"
    ;date.timezone = 修改为
    date.timezone = Asia/Shanghai

自PHP5.4之后 PHP内置了一个Web 服务器。咱们这是PHP7肯定有serve的

    C:\>e:
    e:\>cd website\test
    e:\website\test>php -S localhost:8080
    PHP 7.0.5 Development Server started at Mon Apr 11 14:47:51 2016
    Listening on http://localhost:8080
    Document root is e:\website\test
    Press Ctrl-C to quit.
    [Mon Apr 11 14:48:03 2016] ::1:58855 [200]: /index.php

OK，PHP7已经配置好了；

*注：PHP该内置的Web Server用于个人测试及小网站的开发是没有任何问题的，不是大型应用还是要配合Nginx或Apache以达到最高效率。*

## 解压缩

将下载到的文件解压缩到自己喜欢的位置，例如我自己的位置是`D:\Program Files\mysql-5.7.10-winx64`

## 添加环境变量

右键计算机->属性->高级系统设置->环境变量；在系统变量里找到path，选择编辑，在原有值末尾添加MySQL的bin目录， 例如我的是`D:\Program Files\mysql-5.7.10-winx64\bin;`（注意还是修改是添加，且有分号）（个人经验：放在用户变量的path中，防止改乱整个系统）

## 添加配置文件

在MySQL的安装目录（例如我的是`D:\Program Files\mysql-5.7.10-winx64`）下，建立新文本文件txt，并将其命名为`my.ini`（注意扩展名也要修改）。

（个人经验：将`my-default.ini`直接复制改名就行）

双击打开该文件，并在其中添加内容如下：

    [mysqld]

    basedir=D:\Program Files\mysql-5.7.10-winx64

    datadir=D:\Program Files\mysql-5.7.10-winx64\data

    port = 3306

保存后关闭

## 初始化数据库

以管理员自身份打开CMD执行以下命令（注意必须以管理员身份打开，否则报错）

    mysqld --initialize --user=mysql --console

在控制台消息尾部会出现随机生成的初始密码，记下来（因为有特殊字符，很容易记错，最好把整个消息保存在记事本里）

（个人经验：如果没记下也可以看一下 “计算机名.err” 这个文件，在结尾处用密码）

## 将MySQL添加到系统服务

以管理员自身份打开CMD执行以下命令（注意必须以管理员身份打开，否则报错）

（个人经验：我的电脑转到mysql的解压目录再执行以下命令才好用）

    mysqld --install MySQL
    net start mysql

（个人经验：如果不想安装服务，直接`mysqld --console`启动即可，想删除服务可以用`mysqld --remove`）

安装成功，则显示“服务已启动成功”

## 启动MySQL并修改密码

在CMD控制台里执行命令  `mysql -u root -p`

回车执行后，输入刚才记录的随机密码

执行成功后，控制台显示 `mysql>`，则表示进入`mysql`

输入命令`set password for root@localhost = password(‘123‘);` （注意分号）

(新版的`mysql`数据库下的`user`表中已经没有`Password`字段了而是将加密后的用户密码存储于`authentication_string`字段)

此时`root`用户的密码修改为`123`

END

## 注意事项

以上命令若不能正常运行，则请到安装目录的`bin`文件夹下运行

`my.ini`的位置可以有多处，个人觉得为方便下次修改，应放到安装目录下

+++++++++++++++++

这是我的`my.ini`文件

    # These are commonly set, remove the # and set as required.
    basedir = E:\MySQL5.7
    datadir = E:\MySQL5.7\data
    port = 3306
    # server_id = .....
    explicit_defaults_for_timestamp=true


    # Remove leading # to set options mainly useful for reporting servers.
    # The server defaults are faster for transactions and fast SELECTs.
    # Adjust sizes as needed, experiment to find the optimal values.
    # join_buffer_size = 128M
    # sort_buffer_size = 2M
    # read_rnd_buffer_size = 2M

    # sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

    sql-mode="STRICT_TRANS_TABLES,
      ERROR_FOR_DIVISION_BY_ZERO,
      NO_AUTO_CREATE_USER,
      NO_AUTO_VALUE_ON_ZERO,
      NO_ENGINE_SUBSTITUTION,
      NO_ZERO_DATE,
      NO_ZERO_IN_DATE,
      ONLY_FULL_GROUP_BY"
