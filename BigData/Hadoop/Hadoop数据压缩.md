---
aliases: 
title: Hadoop数据压缩
date created: 2024-03-31 15:03:00
date modified: 2024-03-31 20:03:75
tags: [code/big-data]
---
## 概述
### 优缺点
优点：以减少磁盘IO、减少磁盘存储空间。
缺点：增加CPU开销。

### 压缩原则
1. 运算密集型的 Job，少用压缩
2. IO 密集型的 Job，多用压缩

## MapReduce支持的压缩编码
### 压缩算法对比介绍
![CleanShot 2024-03-31 at 20.03.33.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2020.03.33.png)
### 压缩性能的比较
![CleanShot 2024-03-31 at 16.25.50.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2016.25.50.png)

## 压缩位置选择
![CleanShot 2024-03-31 at 16.30.08.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2016.30.08.png)

## 压缩参数配置
### 解压缩算法对应的编码/解码器
![CleanShot 2024-03-31 at 16.34.40.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2016.34.40.png)
### 启用参数
![CleanShot 2024-03-31 at 16.35.50.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2016.35.50.png)
![CleanShot 2024-03-31 at 16.35.49.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2016.35.49.png)


> [!Example] Map端压缩
> 只需提供压缩方法、设置压缩参数即可。
```java
// 开启 map端输出压缩
conf.setBoolean("mapreduce.map.output.compress", true);
// 设置 map端输出压缩方式
conf.setClass("mapreduce.map.output.compress.codec",BZip2Codec.class,CompressionCodec.class);
```


> [!Example] Reduce端压缩
> 只需提供压缩方法、设置压缩参数即可。
```java
// 设置 reduce端输出压缩开启
FileOutputFormat.setCompressOutput(job, true);
// 设置压缩的方式
FileOutputFormat.setOutputCompressorClass(job, BZip2Codec.class);
```
