---
---aliases:
  - 上卷
title: Rollup
date created: 2024-05-16 17:05:00
date modified: 2024-05-16 17:05:20
tags: [code/big-data, code/database]
---
## Rollup上卷
ROLLUP 在多维分析中是“上卷”的意思，即将数据按某种指定的粒度进行进一步聚合(**从细粒度到粗粒度**)。

### 基本概念
在 Doris 中，我们将用户通过建表语句创建出来的表称为 Base 表（Base Table）。Base 表中保存着按用户建表语句指定的方式存储的基础数据。

在 Base 表之上，我们可以创建任意多个 ROLLUP 表。这些 ROLLUP 的数据是基于 Base 表产生的，并且在**物理上是独立存储**的。

ROLLUP 表的基本作用，在于在 Base 表的基础上，获得**更粗粒度**的聚合数据。

### Aggregate 和 Unique 模型中的 ROLLUP