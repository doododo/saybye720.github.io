---
layout:		post
title:		开发过程中常用的设计模式
description: 
category:	blog
---

##单例模式

1.构造函数为非public（防止外部使用new操作符创建对象），单例类不能在其他类中实例化，只能被自身实例化。

2.拥有一个保存类的实例的静态成员变量$_instance;

3.拥有一个访问这个实例的公共静态成员方法

