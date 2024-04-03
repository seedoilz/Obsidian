---
aliases: 
title: Spark Core
date created: 2024-04-02 19:04:00
date modified: 2024-04-03 11:04:30
tags: [code/big-data, input]
---
## [[Spark运行架构]]
### 运行结构
还是标准的 master-slave 架构，外加一个 cluster manager 来做资源分配。
其中，Driver 就是 master，Executor 就是 slave。
![截屏2024-04-03 上午9.26.23.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-03-09-26-25.png)

### 核心组件
#### Driver
- 将用户程序转化为作业（job）
- 在 Executor 之间调度任务 (task)
- 跟踪 Executor 的执行情况
- 通过 UI 展示查询运行情况
>在这个编程过程中没有看到任何关于 Driver 的字眼。

#### Executor
Spark 是集群中 Worker 中的一个 JVM 进程，负责运行具体任务，任务间彼此独立。Spark 启动时就启动，并且伴随整个 Spark 应用的生命周期存在。
- 负责运行组成 Spark 应用的任务，并将结果返回给驱动器进程
- 它们通过自身的块管理器（Block Manager）为用户程序中要求缓存的 RDD 提供内存式存储。RDD 是直接缓存在 Executor 进程内的，因此任务可以在运行时充分利用缓存数据加速运算。

#### ApplicationMaster
Hadoop 用户向 YARN 集群提交应用程序时, 提交程序中应该包含 ApplicationMaster，用于向资源调度器申请执行任务的资源容器 Container，运行用户自己的程序任务 job，监控整个任务的执行，跟踪整个任务的状态，处理任务失败等异常情况。

### 提交流程
>共两种模式，主要区别在于：**Driver 程序的运行节点位置**。
#### Yarn Client 模式
Client 模式将用于监控和调度的 Driver 模块在客户端执行，而不是在 Yarn 中，所以一般用于测试。
*Driver 在任务提交的本地机器上运行。*

#### Yarn Cluster 模式
Cluster 模式将用于监控和调度的 Driver 模块启动在 Yarn 集群资源中执行。一般应用于实际生产环境。
本地机器任务提交后向 ResourceManager 申请启动 ApplicationMaster，Resource 分配 container 后在合适的 NodeManager 上启动 ApplicationMaster，*此时 ApplicationMaster 就是 Driver*。

## [[RDD]]
### 概念
RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是 Spark 中最基本的数据处理模型。代码中是一个抽象类，它代表一个弹性的、不可变、可分区、里面的元素可并行计算的集合。

1. 弹性
	1. 存储的弹性：内存与磁盘的自动切换；
	2. 容错的弹性：数据丢失可以自动恢复；
	3. 计算的弹性：计算出错重试机制；
	4. 分片的弹性：可根据需要重新分片。
2. 数据集：RDD 封装了计算逻辑，并不保存数据
3. 数据抽象：RDD 是一个抽象类，需要子类具体实现
4. 不可变：RDD 封装了计算逻辑，是不可以改变的，想要改变，只能产生新的RDD，在新的RDD 里面封装计算逻辑

### RDD编程
#### RDD创建
##### 从集合（内存）中创建 RDD
```scala
val rdd1 = sparkContext.parallelize(
	List(1,2,3,4)
)
val rdd2 = sparkContext.makeRDD(
	List(1,2,3,4)
)
```
##### 从外部存储创建RDD
```scala
val sparkConf =
	new SparkConf().setMaster("local[*]").setAppName("spark")
val sparkContext = new SparkContext(sparkConf)
val fileRDD: RDD[String] = sparkContext.textFile("input")
fileRDD.collect().foreach(println)
sparkContext.stop()
```
#### RDD并行度与分区
默认情况下，Spark 可以将一个作业切分多个任务后，发送给 Executor 节点并行计算，而能够并行计算的任务数量我们称之为并行度。这个数量可以在构建 RDD 时指定。
#### RDD转化算子
##### Value类型
###### map函数
>将处理的数据逐条进行映射转换，这里的转换可以是类型的转换，也可以是值的转换。

```scala
val dataRDD2: RDD[String] = dataRDD1.map(
	num => {
		"" + num
	}
)
```
###### mapPartitions函数
>将待处理的数据以分区为单位发送到计算节点进行处理，这里的处理是指可以进行任意的处理，哪怕是过滤数据。

```scala
val dataRDD1: RDD[Int] = dataRDD.mapPartitions(
	datas => {
		datas.filter(_==2)
	}
)
```

###### map和mapPartitions的区别
![截屏2024-04-03 上午11.45.58.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-03-11-46-01.png)
