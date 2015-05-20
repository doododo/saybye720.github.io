---
layout:		post
title:		CentOS7配置Nginx+PHP+MySQL过程中遇到的问题
description: CentOS7做本地服务器配置开发环境中一些小问题记录
category:	blog
---

1.解决linux ping: unknown host www.baidu.com

     cat /etc/resolv.conf
     # Generated by NetworkManager


     # No nameservers found; try putting DNS servers into your
     # ifcfg files in /etc/sysconfig/network-scripts like so:
     #
     # DNS1=xxx.xxx.xxx.xxx
     # DNS2=xxx.xxx.xxx.xxx
     # DOMAIN=lab.foo.com bar.foo.com

    vi /etc/resolv.conf
    search localdomain
    nameserver 114.114.114.114
    nameserver 8.8.8.8

2.开放访问80端口

CentOS7中防火墙和运行级管理程序均发生彻底改变了。firewalld代替iptables，systemd代替SystemV init，所以需要重新熟悉相关命令。个人感觉新的程序更加人性化，操作也很容易。 配置文件也都采用了标准的xml格式，放弃了容易出错的老式配置文件。

    firewall-cmd --permanent --add-service=http  (写入配置文件)
    firewall-cmd --permanent --add-service=https
    firewall-cmd --permanent --add-service=mysql  开放3306  //或者开放制定端口firewall-cmd --permanent --zone=public --add-port=3306/tcp 
    firewall-cmd --reload  重新载入
    此时，httpd这个服务添加到了/etc/firewalld/zones/public.xml这个zone配置文件中，所以firewalld才能够据此放行。此文件如下所示:
    
    <?xml version="1.0" encoding="utf-8"?>
    <zone>
      <short>Public</short>
      <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
      <service name="dhcpv6-client"/>
      <service name="http"/>
      <service name="ssh"/>
      <service name="https"/>
    </zone>

    注意不要将此处firewalld管理的service与systemd中的sevice配置单元混淆，两者没有任何关系。

    http服务是安装firewalld时自动安装的，这个服务的配置文件为 /usr/lib/firewalld/services/http.xml，我们来看看这个文件。
    
    <?xml version="1.0" encoding="utf-8"?>
    <service>
      <short>WWW (HTTP)</short>
      <description>HTTP is the protocol used to serve Web pages. If you plan to make your Web server publicly available, enable this option. This option is not required for viewing pages locally or developing Web pages.</description>
      <port protocol="tcp" port="80"/>
    </service>
    
    据此知道http服务对应的就是tcp协议的80端口。firewalld根据zone配置文件中的服务名http,依次在/etc/firwalld/services/目录，/usr/lib/firewalld/services/目录查找名为http.xml的文件，找到即停止继续查找，所以位于/etc/firwalld/services/目录的配置文件优先级更高。

    关闭防火墙

    systemctl stop firewalld

3.安装PHP，使PHP支持 MariaDB

    yum install php-mysql php-gd libjpeg* php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-bcmath php-mhash

配置PHP


4.安装MariaDB

    yum install mariadb mariadb-server #询问是否要安装，输入Y即可自动安装,直到安装完成
    systemctl start mariadb.service #启动MariaDB
    systemctl stop mariadb.service #停止MariaDB
    systemctl restart mariadb.service #重启MariaDB
    systemctl enable mariadb.service #设置开机启动
    cp /usr/share/mysql/my-huge.cnf /etc/my.cnf #拷贝配置文件（注意：如果/etc目录下面默认有一个my.cnf，直接覆盖即可）

    设置MariaDB帐户密码
    
    mysql_secure_installation
    
    回车，根据提示输入Y
    输入2次密码，回车
    根据提示一路输入Y
    最后出现：Thanks for using MySQL!
    MySql密码设置完成，重新启动 MySQL：
    
    systemctl restart mariadb.service #重启MariaDB

    开启远程访问数据库
    1.链接数据库
    2.use mysql;
    3.update user set host='%' where user = 'root';
    4.重启mysql，即可连上
    或者：
    grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
    # root是用户名，%代表任意主机，'123456'指定的登录密码（这个和本地的root密码可以设置不同的，互不影响）
    flush privileges; # 重载系统权限

5.centos7查看哪些用户

    who
    root     pts/0        2015-03-30 12:44 (192.168.1.111)
    root     pts/1        2015-03-30 12:28 (192.168.1.111)
    root     pts/2        2015-03-30 12:44 (192.168.1.111)
    //踢掉某个连接
    pkill -kill -t pts/0

6.关于centos7安装Mariadb后无法链接问题（强制重置密码）
  
    systemctl stop mariadb.service   (service mysqld stop )   
    /usr/bin/mysqld_safe --skip-grant-tables   
    //另外开个SSH连接   
    [root@localhost ~]# mysql   
    mysql>use mysql   
    mysql>update user set password=password("123456") where user="root";   
    mysql>flush privileges;   
    mysql>exit   
    //pkill -KILL -t pts/0 可将pts为0的用户(之前运行mysqld_safe的用户窗口)强制踢出   
    //重启mysql即可   

注：查看mysql进程

    //ps -A | grep mysql   

    //关闭的话直接kill 进程id即可   

7.设置默认启动

    service enable httpd.service //设置Apache默认开机启动   
    service enable mariadb.service //设置MariaDB默认开机启动.   

8.启动SSH

    ssh systemctl start sshd.service

9.查看selinux状态
  
  getenforce

关闭SELinux：

-临时关闭（不用重启机器）：

  setenforce 0     ##设置SELinux 成为permissive模式

                   ##setenforce 1 设置SELinux 成为enforcing模式

-修改配置文件需要重启机器：

-修改/etc/selinux/config 文件

-将SELINUX=enforcing改为SELINUX=disabled

-重启机器即可

10.关于centos  yum

-使用YUM查找软件包

-命令：yum search

-列出所有可安装的软件包

-命令：yum list

-列出所有可更新的软件包

-命令：yum list updates

-列出所有已安装的软件包

-命令：yum list installed

-列出所有已安装但不在 Yum Repository 內的软件包

-命令：yum list extras

-列出所指定的软件包

-命令：yum list 7.使用YUM获取软件包信息

-命令：yum info 8.列出所有软件包的信息


-命令：yum info

-列出所有可更新的软件包信息

-命令：yum info updates

-列出所有已安裝的软件包信息

-命令：yum info installed

-列出所有已安裝但不在 Yum Repository 內的软件包信息

-命令：yum info extras

-列出软件包提供哪些文件

-命令：yum provides

-清除YUM缓存

-yum 会把下载的软件包和header存储在cache中，而不会自动删除。如果我们觉得它们占用了磁盘空间，可以使用yum clean指令进行清除，更精确的用法是yum clean headers清除header，yum clean packages清除下载的rpm包，yum clean all 清除所有

-清除缓存目录(/var/cache/yum)下的软件包

-命令：yum clean packages

-清除缓存目录(/var/cache/yum)下的 headers

-命令：yum clean headers

-清除缓存目录(/var/cache/yum)下旧的 headers

-命令：yum clean oldheaders

-清除缓存目录(/var/cache/yum)下的软件包及旧的headers

-命令：yum clean, yum clean all (= yum clean packages; yum clean oldheaders)
