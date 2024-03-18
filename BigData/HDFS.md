---
aliases: 
title: HDFS架构概述
date created: 三月 12日 2024, 1:36:05 下午
date modified: 三月 18日 2024, 6:58:31 晚上
tags:
  - code/big-data
  - input
---
# [[HDFS架构概述]]
>Hadoop Distributed File System，简称 HDFS，是一个分布式文件系统。
![CleanShot 2024-03-18 at 13.13.10@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2013.13.10%402x.png)
![CleanShot 2024-03-18 at 13.13.34@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2013.13.34%402x.png)
1. NameNode（nn）：存储文件的元数据，如文件名，文件目录结构，文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的DataNode等。
2. DataNode(dn)：在本地文件系统存储文件块数据，以及块数据的校验和。
3. Secondary NameNode(2nn)：每隔一段时间对NameNode元数据备份。

**使用场景**：适合一次写入，多次读出的场景。一个文件经过创建、写入和关闭之后就不需要改变。

## HDFS优缺点
### 优点
1. 高容错性
2. 适合处理大数据
3. 可构建在廉价机器上，通过多副本机制，提高可靠性。

### 缺点
1. 不适合低延时数据访问
2. 无法高效的对大量小文件进行存储
3. 不支持并发写入、文件随机修改。
	1. 一个文件只能有一个写，不允许多个线程同时写
	2. 仅支持数据append（追加），不支持文件的随机修改

## [[HDFS文件块]]
>[[HDFS]]中的文件在物理上是分块存储 (Block)，块的大小可以通过配置参数(dfs.blocksize)来规定，默认大小在Hadoop2.x/3.x版本中是128M，1.x版本中是64M。

***寻址时间为传输时间的1%时，则为最佳状态。（专家）***

### 为什么块的大小不能设置太小，也不能设置太大？
1. HDFS的块设置太小，会增加寻址时间，程序一直在找块的开始位置；
2. 如果块设置的太大，从磁盘传输数据的时间会明显大于定位这个块开始位置所需的时间。导致程序在处理这块数据时，会非常慢。

## [[HDFS读写流程]]
### 文件写入
![CleanShot 2024-03-18 at 17.05.47@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2017.05.47%402x.png)
#### 步骤
1. 客户端通过 Distributed FileSystem 模块向 NameNode 请求上传文件，NameNode 检查目标文件是否已存在，父目录是否存在。
2. NameNode 返回是否可以上传。
3. 客户端请求第一个 Block 上传到哪几个 DataNode 服务器上。
4. NameNode 返回3个DataNode 节点，分别为dn1、dn2、dn3。
5. 客户端通过 FSDataOutputStream 模块请求dn1 上传数据，dn1 收到请求会继续调用dn2，然后dn2 调用dn3，将这个通信管道建立完成。
6. dn1、dn2、dn3 逐级应答客户端。
7. 客户端开始往dn1 上传第一个Block （先从磁盘读取数据放到一个本地内存缓存），以 Packet 为单位，dn1 收到一个 Packet 就会传给 dn2，dn2 传给 dn3；dn1 每传一个 packet会放入一个应答队列等待应答。
8. 当一个 Block 传输完成之后，客户端再次请求 NameNode 上传第二个 Block 的服务器。

### 网络拓扑-节点距离计算
![CleanShot 2024-03-18 at 17.32.06@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2017.32.06%402x.png)

### 副本节点选择
![CleanShot 2024-03-18 at 18.47.01@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2018.47.01%402x.png)

### HDFS读流程
![CleanShot 2024-03-18 at 18.57.41@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-18%20at%2018.57.41%402x.png)
