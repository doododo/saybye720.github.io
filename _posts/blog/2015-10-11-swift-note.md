---
layout:     post
title:      Swift学习笔记1-上手
category: blog
description: 随着IPhone6的发布，Swift应用从此备受关注，好久没有学习新语言了，跟上节奏吧
---

## 整数和浮点数

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

## 字符、Unicode和字符串

在Swift里，String是否允许被修改，只取决于let和var来定义，当我们把String对象赋值给其他变量的时候，Swift会完整复制字符串的内容，而不是复制一个指向原有字符串的引用。当然Swift对字符串的拷贝操作进行了优化，只有当后面代码真正修改字符串的时候，拷贝操作才真正发生.

## 便捷的Tuple类型

很多时候用到Tuple去处理，例如http返回状态码，信息，或者返回一条记录。

- 状态码: 200; 状态消息: HTTP OK
- 状态码: 404; 状态消息: File not found
- 姓名: Mars; 工号: 11; 电子邮件: 11@boxue.io

```
let message = (200, "HTTP OK")
let fileNotFound = (404, "File not found")
let me = (name:"bigface", age:28, qq:"82220797")
```

访问Tuple成员

```
message.0 // 200
fileNotFound.1 // File not found
me.name //bigface
```

我们在定义Tuple的时候，还可以把一个Tuple的值，一一对应的拆分到不同的变量上，这叫做Tuple Decomposition。例如，对于我们之前 定义过的success，我们可以这样定义一个新的Tuple：

```
var (status, msg) = message
print(status)  //200
print(msg)   //HTTP OK
status = 404 // 404
message // (.0 200,.1 "HTTP OK")
```

虽然同构status和msg可以轻松的访问和修改我们的定义的Tuple成员，但是message并不受影响,如果我们只是想对应到Tuple中特定的成员，而忽略其它成员，我们可以使用下划线'_'来代表那些不需要被对应的成员。例如：

```
let (_, errorMessage) = fileNotFound
print(errorMessage)  //  File not found
```

## 常用操作符

基本和其他语言一样，直接举几个例子吧。

```swift
// 赋值运算
let a = 20
var b = 30

//算数运算符
let sum = a + b
var div = a / b

/*
加、减、乘、除运算没有任何意外。在这里要特别说明一下的是“%”，和C不同，Swift允许我们对浮点数使用%运算,当我们对浮点数取模的时候，会得到一个对应的浮点数。
 */
let mod = 8 % 2.5    //0.5

// 复合运算符
b +=10      //b = b + 10
b *=10      //b = b * 10
b %=10      //b = b % 10

/*
Swift不会把数字自动转换成Bool类型。在需要Bool值的地方，你必须明确使用一个Bool变量。
 */

// 自增、自减
var ppb = b++
var bpp = --b

// 比较操作符
let isEqual = sum == 10
let isNotEqual = sum != 10
let is Less = sum >= 10

/*
除此之外，Swift还支持两个用于面向对象的比较操作符：Identity operator，它们用来判断两个操作数是否引用同一个对象。
//: Identity operator
// ===
// !==
 */

//三元操作符
if condition {
    expression1
}else{
    expression2
}
let isEqual ? expression1 : expression2

// Nil Coalescing Operator 这是一个Swift特有的操作符，用来处理和Optional有关的判断,如果opt是一个optional，当其不为Nil时，自动解引用，否则，使用“默认值”b。
var userInput: String? = "A user input"
let value = userInput ?? "A default input"

//Range operator
//[begin,end]闭区间 ...; [begin,end)半开半闭区间 ..<
for index in 1...5 {
    print(index)
}

// 逻辑运算符
let logicalNot = !isEqual
let logicalAnd = isNotEqual && isLess
let logicalOR  = isGreater  || (isLess && isLe)
```


