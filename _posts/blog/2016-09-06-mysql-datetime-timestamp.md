---
layout:   post
title:    关于Mysql中Datetime和Timestamp分析
description: mysql中用于表示时间的三种类型date, datetime, timestamp (如果算上int的话，四种) 比较容易混淆
category: blog
---

MySQL常用的时间存储类型就是 `Int`,`datetime`,`timestamp`，这三种都可用于存储时间。

> - DATETIME 类型用于保存同时包含日期和时间两部分的值。MySQL 以 ‘YYYY-MM-DD HH:MM:SS’ 形式接收和显示 DATETIME 类型的值。
> - TIMESTAMP 类型用于保存同时包含日期和时间两部分的值。
> - DATETIME 或 TIMESTAMP类型的值可以在尾部包含一个毫秒部分，精确度最高到微秒（6 位数）。
> - TIMESTAMP 和 DATETIME 数据类型提供自动初始化和更新到当前的日期和时间的功能，只需在列的定义中设置 DEFAULT CURRENTTIMESTAMP 和 ON UPDATE CURRENTTIMESTAMP。
> - Int存储从1970-01-01 00:00:00开始后的毫秒数，如果进行日期比较要进行复杂的转换。
> - Timestamp 和 Datetime 的核心不同点主要在于 MySQL 在内部如何表示这些信息：两种都以二进制而非字符串形式存储，但在表示日期/时间部分时 Timestamp （4 字节） 比 Datetime （5 字节） 少使用 1 字节。当保存毫秒部分时两种都使用额外的空间 （1-3 字节）
> - 只能存储从 ‘1970-01-01 00:00:01.000000’ 到 ‘2038-01-19 03:14:07.999999’ 之间的时间。而 Datetime 允许你存储从 ‘1000-01-01 00:00:00.000000’ 到 ‘9999-12-31 23:59:59.999999’ 之间的任何时间。
> - Timestamp 是 MySQL 使用服务器的时区转换 Timestamp 值到它的 UTC 等价值再保存，获取值时也会进行转换，而 Datatime 无论失去怎么配置，保存的值和获取到的值相同。

总结：

基于这些数据，我确信 Datetime 是大多数场景下的最佳选择。原因是：

> - 更快。
> - 无需任何转换即是人类可读的。
> - 不会因为时区变换产生问题。
> - 只比它的对手们多用 1 字节
> - 支持更大的日期范围（从 1000 年到 9999 年）
> - 如果你只是存储 Unix 时间戳（并且在它的合法日期范围内），而且你真的不打算在它上面使用任何基于日期的查询，我觉得使用 INT 是可以的。我们已经看到，它执行简单数值比较查询时非常快，因为只是在处理简单的数字。

Timestamp 怎么样呢？如果 Datetime 相对于 Timestamp 的优势不适用于你特殊的场景，你最好使用时间戳。

*至于Laravel中`created_at`，`updated_at`等字段类型默认是`timestamp`，我客观认为这是这几个字段只是为了记录创建时间，更新时间等，所以Laravel直接利用 MySQL 的 `CURRENT_TIMESTAMP` 方法，从数据库层面去维护这几个时间字段*
