---
layout:     post
title:      Swift学习笔记1-上手
category: blog
description: 随着IPhone6的发布，Swift应用从此备受关注，好久没有学习新语言了，跟上节奏吧
---

##整数和浮点数

- Int来定义有符号整数，UInt定义无符号证书

可以使用分隔符 例如：`let iphonePrice = 4_999`

* “除非我们所在的硬件平台有特别明确的需求需要我们使用UInt来定义无符号整数， 我们总是应该尽可能使用Int来表达所有的整数类型，哪怕我们确定一个整数一定是一个非负数。 这会给我们减少很多不必要的类型转换的麻烦。” *

- Float:最多表达6位精度的浮点数；Double:最多可以表达15位的浮点数

* “如不是有明确的需求，我们应该统一使用Double来定义浮点数。” *

`dynamictType`:可以查看一个变量的类型

```
var PI = 3 + 0.14
PI.dynamictType         //Double.Type
```

在Swift里不同类型的变量无法相加
```
var PI = 3.14
var num = 1
PI + Double(num)
```

`Double(变量)`把一个Int类型“转换”成浮点型，并没有真的把num的类型从Int转换成Double，而是用num的值，初始化了一个新的值为1的Double，并用这个新的Double和PI相加。

##字符、Unicode和字符串

在Swift里，String是否允许被修改，只取决于let和var来定义，当我们把String对象赋值给其他变量的时候，Swift会完整复制字符串的内容，而不是复制一个指向原有字符串的引用。当然Swift对字符串的拷贝操作进行了优化，只有当后面代码真正修改字符串的时候，拷贝操作才真正发生.


