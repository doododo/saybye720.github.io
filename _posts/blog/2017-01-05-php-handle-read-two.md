---
layout: post
title: 重新阅读PHP手册（二）
category: blog
description: 关于PHP语法问题
---

## 类型强制转换

允许强制转换的类型有：

*   (int),(integer) - 转换为整形
*   (bool),(boolean) - 转换为布尔型
*   (float),(double),(real) - 转换为浮点型
*   (string) - 转换为字符串
*   (array) - 转换为数组
*   (ojbect) - 转换为对象
*   (unset) - 转换为NULL
*   (binary) - 转换为二进制 同b前缀
> eg.
> 将一个整形转换为字符串的多种方式

```php
$foo = 100;
$str = "$foo";
$fst = (string) $foo; // 双引号可以将变量转换为字符串，同(sting)功能
```
