---
aliases: 
title: MapReduce框架原理
date created: 2024-03-24 09:03:00
date modified: 2024-03-25 10:03:66
tags: [code/big-data]
---
## InputFormat 数据输入
- 数据块：Block 是 HDFS 物理上把数据分成一块一块。数据块是 HDFS 存储数据单位。
- 数据切片：只是在逻辑上对输入进行分片，并不会在磁盘上将其切分成片进行存储。数据切片是MapReduce 程序计算输入数据的单位，一个切片会对应启动一个MapTask。

### 数据切片与MapTask并行度决定机制
1. **一个Job的Map阶段并行度由客户端在提交Job时的切片数决定**
2. 每一个Split切片分配一个MapTask并行实例处理
3. 默认情况下，**切片大小=BlockSize**
4. 切片时不考虑数据集整体，而是逐个针对每一个文件单独切片

#### 面试重点
1. 切片时不考虑数据集整体，而是逐个对于每一个**文件** *单独*切片。
2. 切片大小可以调整，因为 `切片大小=max(blockSize, min(minSize, maxSize))`。
3. **切片大小**可能会超过*设置的切片大小*，设置了1.1倍（32.1MB切一片，避免产生小文件）

#### 源码解析
![Capture 2024-03-24 at 19.35.52@2x.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/Capture%202024-03-24%20at%2019.35.52%402x.png)

### 不同的实现类
#### TextInputFormat
>默认FileInputFormat实现类

键是存储该行在整个文件中的起始字节偏移量， LongWritable 类型。值是这行的内容，不包括任何行终止符（换行符和回车符），Text 类型。

#### CombineTextInputFormat
>TextinputFormat会让**小文件**也产生一个单独的切片，在大量小文件的情况下，会产生大量的MapTask，导致浪费资源。
>而CombineTextInputFormat可以解决此问题，多个小文件在逻辑上划分到一个切片中。

##### 虚拟存储切片最大值设置
```java
CombineTextInputFormat.setMaxInputSplitSize(job, 4194304);// 4m
```
##### 切片机制
生成切片过程包括：虚拟存储过程和切片过程二部分。
![Capture 2024-03-24 at 20.16.30.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/Capture%202024-03-24%20at%2020.16.30%402x.png)

###### 虚拟存储过程
将输入目录下所有文件大小，依次和设置的 setMaxInputSplitSize 值比较，如果不大于设置的最大值，逻辑上划分一个块。如果输入文件大于设置的最大值且大于两倍，那么以最大值切割一块；当剩余数据大小超过设置的最大值且不大于最大值2 倍，此时将文件均分成 2 个虚拟存储块（防止出现太小切片）。

###### 切片过程
1. 判断虚拟存储的文件大小是否大于 setMaxInputSplitSize 值，大于等于则单独形成一个切片。
2. 如果不大于则跟下一个虚拟存储文件进行合并，共同形成一个切片。

## MapReduce工作原理
![CleanShot 2024-03-25 at 09.24.43.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-25%20at%2009.24.43.png)
![CleanShot 2024-03-25 at 09.47.54.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-25%20at%2009.47.54.png)

## Shuffle机制
>Map 方法之后，Reduce 方法之前的数据处理过程称之为Shuffle。


1. Map方法执行完成后，context.write(), 直接输出我们的Key-value
2. 环形缓冲区默认大小是100MB，在80%的时候进行反向，来进行溢写（写入磁盘），相当于一个线程在写入内存，而另一个线程在写入磁盘。
3. 在溢写的时候，会按照key进行分区，对80MB（80%）空间内的数据进行Key排序（**快排**），只修改索引。