---
aliases: 
title: Spark Core
date created: 2024-04-02 19:04:00
date modified: 2024-04-03 17:04:79
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

> [!NOTE] map和mapPartitions的区别
> ![截屏2024-04-03 上午11.45.58.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-03-11-46-01.png)
###### flatMap函数
> 将处理的数据进行扁平化后再进行映射处理，所以算子也称之为扁平映射

###### glom函数
>将同一个分区的数据直接转换为相同类型的内存数组进行处理，分区不变

###### groupBy函数
>将数据根据指定的规则进行分组, 分区默认不变，但是数据会被打乱重新组合，我们将这样的操作称之为shuffle。极限情况下，数据可能被分在同一个分区中
```scala
val dataRDD = sparkContext.makeRDD(List(1,2,3,4),1)
val dataRDD1 = dataRDD.groupBy(
	_%2
)
```
###### filter函数
>将数据根据指定的规则进行筛选过滤，符合规则的数据保留，不符合规则的数据丢弃。当数据进行筛选过滤后，分区不变，但是分区内的数据可能不均衡，生产环境下，可能会出现数据倾斜。

###### coalesce函数
>根据数据量缩减分区，用于大数据集过滤后，提高小数据集的执行效率。当spark 程序中，存在过多的小任务的时候，可以通过 coalesce 方法，收缩合并分区，减少分区的个数，减小任务调度成本。
```scala
val dataRDD1 = dataRDD.coalesce(2)
```

###### repartition
>该操作内部其实执行的是 coalesce 操作，参数shuffle 的默认值为true。无论是将分区数多的RDD 转换为分区数少的RDD，还是将分区数少的 RDD 转换为分区数多的RDD，repartition操作都可以完成。
```scala
val dataRDD1 = dataRDD.repartition(4)
```

##### 双Value类型
- intersection
- union
- subtract
- zip

##### Key-Value类型
###### partitionBy函数
>将数据按照指定Partitioner 重新进行分区。Spark 默认的分区器是HashPartitioner
```scala
val rdd2: RDD[(Int, String)] =
	rdd.partitionBy(new HashPartitioner(2))
```

###### reduceByKey函数
>可以将数据按照相同的Key 对Value 进行聚合
```scala
val dataRDD1 = sparkContext.makeRDD(List(("a",1),("b",2),("c",3)))
val dataRDD2 = dataRDD1.reduceByKey(_+_)
val dataRDD3 = dataRDD1.reduceByKey(_+_, 2)
```

###### groupByKey
>将数据源的数据根据 key 对 value 进行分组
```scala
val dataRDD2 = dataRDD1.groupByKey()
val dataRDD3 = dataRDD1.groupByKey(2)
val dataRDD4 = dataRDD1.groupByKey(new HashPartitioner(2))
```

> [!NOTE] reduceByKey 和 groupByKey 的区别？
> ![截屏2024-04-03 下午4.06.48.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-03-16-06-52.png)

###### aggregateByKey函数
>将数据根据不同的规则进行分区内计算和分区间计算
>第一个参数：如何在同一个分区中对同一个键的多个值进行聚合
>第二个参数：如何在不同分区之间聚合已经聚合过的结果

###### foldByKey函数
>当aggregateByKey函数的第一个参数和第二个参数相同时

###### combineByKey函数
>createCombiner：当一个键第一次出现时，它会使用这个函数来创建该键的累加器的初始值。这个函数会对键的第一个值应用，并返回一个新的值（可能是转换过的），这个新的值会作为累加器的初始值。
>mergeValue：对于同一个键，在同一个分区中，当这个键后续出现时，使用这个函数来将该键的新值与累加器的当前值合并。
>mergeCombiners：在不同分区中，当同一个键的累加器需要合并时，使用这个函数来合并这些累加器的值。

###### cogroup
>在类型为(K,V)和(K,W)的RDD 上调用，返回一个(K,(Iterable<V>,Iterable<W>))类型的RDD
