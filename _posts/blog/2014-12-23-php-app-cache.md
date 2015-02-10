---
layout:		post
title:		PHP开发App接口之缓存
category:	blog
description:	用Memcache和Redis设置缓存
---

##Memcache和Redis异同

同：两者都可以用来管理数据，数据都是放在内存中

异：Redis可定期将数据备份到磁盘（持久化）；Memercache只能简单的Key/Value存储，Redis不仅支持Key/Value存储，还提供list，set，hash等数据结构的存储


