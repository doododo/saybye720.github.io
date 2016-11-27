---
layout:   post
title:    mysql常见错误整理
description: 工作和学习中遇到常见的mysql报错以及处理方式
category: blog
---

- `PDOException: SQLSTATE[HY000]: General error: 1366 Incorrect integer value: '' for column 'parent_id'`

eg.

```
// 表结构

Create Table

CREATE TABLE `menus` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `category` varchar(100) COLLATE utf8_unicode_ci NOT NULL COMMENT '分类',
  `parent_id` smallint(5) unsigned DEFAULT NULL COMMENT '父ID',
  `order` int(10) unsigned DEFAULT NULL COMMENT '排序,数字越小越靠前',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=20 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci

```

问题：当插入顶级分类的时候，表单`parent_id`的`value=''`，此时mysql就会报错：`parent_id`值不是一个int类型

解决思路：PHP获取`parent_id`的值，如果为空，强制赋值null.

```
$category->parent_id = $request->parent_id ?: null;
```

- PHP7中`??`并不能替换所有三元运算符

eg.

```
// first
$b = $a ?? 1;

// second
$b = $a ?: 1;

// third
$b = $a ? $a : 1;
```

第二种和第三种方式用到任何地方都是一样的结果，第一种当$a为''时候，和后面产生不一样的结果。php7的`??`三目运算符只是判断了`isset()`

![PHP三元运算符不要瞎用](http://7xttyt.com1.z0.glb.clouddn.com/PHP7三目运算符.png-github.blog)

