---
layout:		post
title:		Mac下关于Android Studio的使用
category:	blog
description:	刚开始学习Android开发，赶上Android出自己的开发工具了

---
##您需要安装旧 Java SE 6 运行环境才能打开“Android Studio”

Mac升级后Android Studio提示这个错误，解决办法：不用再安装jdk1.6了。

1. 用文本编辑器打开/Applications/Android Studio/Contents/Info.plist

2. 搜索JVMVersion，将其值改为1.7*

3. 再次运行应用即可看到应用成功运行 
