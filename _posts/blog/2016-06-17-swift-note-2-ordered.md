---
layout:     post
title:      Swift学习笔记2-集合
category: blog
description: Collection是一种存放数据的“容器”。Swift默认提供了三种Collection: Array, Set和Dictionary。它们有着类似的接口，却有着各自不同的用处
---

## Ordered 有序集合

每一个字符都有对应的位置

```
let boxue = ["B", "o", "x", "u", "e"]
boxue.1    // o
```

## Unordered 无序集合

每一个元素之间没有位置关系，唯一有意义的，就是字符的值。

## Array

三种方法定义一个空数组

```
// 和C++ STL类似的方式：
var array1: Array<Int> = Array<Int>()
// 类似C语言数组的方式：
var array2: [Int] = array1
//使用一个array来初始化另一个array
var array3 = array2

```

我们还可以为数组指定初始值

```
var threeInts = [Int](count: 3, repeatedValue: 1)   //  [1, 1, 1]  包含一组重复的数值
var countInts = threeInts + threeInts   //  [1, 1, 1, 1, 1, 1]  两个数组相加生成一个新的数组
var fourInts = [1, 2, 3, 4]    //   [1, 2, 3, 4]    像C语言一样，为数组明确指定每一个元素的值
```

访问和修改数组元素

```
fourInts.count //   4 直接访问数组对象的count属性
```
判断数组是否为空

```
fourInts.isEmpty //  false
```

向数组末尾添加元素

```
fourInts.append(1)  // [1, 2, 3, 4, 1]
fourInts += [7, 8, 9]// [1, 1, 1, 1, 7, 8, 9] 通过+=操作符拼接数组
```

使用索引

```
threeInt[6] // 9
threeInt[4...5] // [7, 8]   swift可以使用范围索引
/*
当我们给数组赋值时，如果Range操作符引用的元素多于赋值提供的元素，原数组中剩余的元素会被删除
 */

```
