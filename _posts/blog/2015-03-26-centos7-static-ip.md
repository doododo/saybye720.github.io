---
layout:		post
title:		CentOS之静态IP设置
description: 本地服务器配完CentOS7后发现IP是DHCP自动获取的，给本地开发带来不便，还是配置下静态IP好些。
category:	blog
---
1.编辑ifcfg-em1 文件，vim 最小化安装时没有被安装，需要自行安装不描述。

    # vim /etc/sysconfig/network-scripts/ifcfg-em1

2.修改如下内容

    BOOTPROTO="static" #dhcp改为static
    ONBOOT="yes" #开机启用本配置
    IPADDR=192.168.1.100 #静态IP
    GATEWAY=192.168.1.1 #默认网关
    NETMASK=255.255.255.0 #子网掩码
    DNS1=114.114.114.114 #DNS 配置

3.查看修改后效果

    cat /etc/sysconfig/network-scripts/ifcfg-em1
    HWADDR="B0:83:FE:C3:D7:BB"
    TYPE="Ethernet"
    BOOTPROTO="static"
    DEFROUTE="yes"
    PEERDNS="yes"
    PEERROUTES="yes"
    IPV4_FAILURE_FATAL="no"
    IPV6INIT="yes"
    IPV6_AUTOCONF="yes"
    IPV6_DEFROUTE="yes"
    IPV6_PEERDNS="yes"
    IPV6_PEERROUTES="yes"
    IPV6_FAILURE_FATAL="no"
    NAME="em1"
    UUID="e6af4aa6-0fa0-45c0-9437-1002766740d2"
    ONBOOT="yes"
    IPADDR=192.168.1.100
    GATEWAY=192.168.1.1
    NETMASK=255.255.255.0
    DNS=114.114.114.114

4.重启网络服务

    service network restart

5.查看改动后的效果

    ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    2: em1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
        link/ether b0:83:fe:c3:d7:bb brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.100/24 brd 192.168.1.255 scope global em1
           valid_lft forever preferred_lft forever
        inet6 fe80::b283:feff:fec3:d7bb/64 scope link
           valid_lft forever preferred_lft forever
    3: em2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN qlen 1000
        link/ether b0:83:fe:c3:d7:bc brd ff:ff:ff:ff:ff:ff

完成任务，CentoOS 7 不再使用 ifconfig 而是用 ip 命令查看网络信息。
