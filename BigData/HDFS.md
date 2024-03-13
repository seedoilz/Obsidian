---
aliases: 
title: HDFS
date created: 三月 12日 2024, 1:36:05 下午
date modified: 三月 12日 2024, 1:47:18 下午
tags:
  - code/big-data
---
## 架构概述
>Hadoop Distributed File System，简称 HDFS，是一个分布式文件系统。

1. NameNode（nn）：存储文件的元数据，如文件名，文件目录结构，文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的DataNode等。
2. DataNode(dn)：在本地文件系统存储文件块数据，以及块数据的校验和。
3. Secondary NameNode(2nn)：每隔一段时间对NameNode元数据备份。