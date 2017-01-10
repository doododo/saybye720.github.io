---
layout: post
title: 重新阅读PHP手册（二）
category: blog
description: 关于PHP语法中的类型和变量
---

## 类型强制转换

允许强制转换的类型有：

- (int),(integer) - 转换为整形
- (bool),(boolean) - 转换为布尔型
- (float),(double),(real) - 转换为浮点型
- (string) - 转换为字符串
- (array) - 转换为数组
- (ojbect) - 转换为对象
- (unset) - 转换为NULL
- (binary) - 转换为二进制 同b前缀

eg.将一个整形转换为字符串的多种方式

```php
$foo  = 100;
$str1 = "$foo";
$str2 = (string) $foo; // 双引号可以将变量转换为字符串，同(sting)功能
```

## PHP变量

- `$this` 是一个特殊的变量，不能被赋值
- 变量名不能以数字开头，可以为中文（尽量别干这种事）
- `$GLOBALS`  引用*全局*作用域中可用的全部变量,变量的名字就是数组的键，也解释为[超级全局变量]

eg.（出自php手册）

```php

function test()
{
    $foo = "hello";

    echo '$foo in global scope: ' . $GLOBALS["foo"] . "\n";
    echo '$foo in current scope: ' . $foo . "\n"; 
}

$foo = "world";
test();

// 结果
// $foo in global scope: world
// $foo in current scope: hello
```

- 如何判断可变变量执行顺序

eg.

```php
class foo {
    var $bar = 'I am bar.';
    var $arr = array('I am A.', 'I am B.', 'I am C.');
    var $r   = 'I am r.';
}

$foo = new foo();
$arr = 'arr';
echo $foo->$arr[1] . "\n"; // 先执行$arr[1] ==> $foo->r  ==> I am r.
echo $foo->{$arr}[1] . "\n"; // 先执行$foo->$arr ==> $arr[1] ==> I am B.
```

*在 PHP 的函数和类的方法中，超全局变量不能用作可变变量。$this 变量也是一个特殊变量，不能被动态引用。*

eg2.

```html
<input type="image" src="image.gif" name="sub">

<!--
咋一看这个没有任何值得提地方，就是把提交按钮替换成一个图片，表单传递到服务器时，自动添加了2个变量，`sub_x`和`sub_y`，也就是用户点击图片的坐标。但浏览器发送的明明是`sub.x`和`sub.y`？？？

PHP不仅会将变量中的.转换为下划线，也包括空格、下划线，开放方括号(或者说中括号)
-->
```
