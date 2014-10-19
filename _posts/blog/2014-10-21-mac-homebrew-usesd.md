---
layout:		post
title:		Mac OS X 下 Homebrew 的使用
category:	blog
description:	Homebrew尽可能地利用系统自带的各种库，使得软件包的编译时间大为缩短；同时由于几乎不会造成冗余，软件包的管理也清晰、灵活了许多

---

Homebrew的可执行命令是brew，其基本使用方法如下（以wget为例）。

查找软件包

	brew search wget

安装软件包

	brew install wget

列出已安装的软件包

	brew list

删除软件包

	brew remove wget

查看软件包信息

	brew info wget

列出软件包的依赖关系

	brew deps wget

更新brew

	brew update

列出过时的软件包（已安装但不是最新版本）

	brew outdated

更新过时的软件包（全部或指定）

	brew upgrade 或 brew upgrade wget

定制自己的软件包

如果自己需要的软件包并不能在Homebrew中找到，怎么办呢，毕竟Homebrew是一个新生项目，不可能满足所有人的需求。当然，我们可以自行编译安装，但手工安装的软件包游离于Homebrew之外，管理起来不是很方便。

前文说过，Homebrew使用Ruby实现的软件包配置非常方便，下面简单谈一谈软件包的定制（假定软件包名称是bar，来自foo站点）。

首先找到待安装软件的源码下载地址

http://foo.com/bar-1.0.tgz

建立自己的formula

brew create http://foo.com/bar-1.0.tgz

编辑formula，上一步建立成功后，Homebrew会自动打开新建的formula进行编辑，也可用如下命令打开formula进行编辑。

	brew edit bar

Homebrew自动建立的formula已经包含了基本的configure和make install命令，对于大部分软件，不需要进行修改，退出编辑即可。

输入以下命令安装自定义的软件包