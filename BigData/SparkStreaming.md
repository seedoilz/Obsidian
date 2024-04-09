---
aliases:
  - Spark Streaming
title: SparkStreaming
date created: 2024-04-09 11:04:00
date modified: 2024-04-09 14:04:54
tags: [code/big-data]
---
## 概念
>SparkStreaming是**准实时**，**微批次**的数据处理框架。

和 Spark 基于 RDD 的概念很相似，Spark Streaming 使用离散化流(discretized stream)作为抽象表示，叫作DStream。DStream 是随时间推移而收到的数据的序列。在内部，每个时间区间收到的数据都作为 RDD 存在，而DStream 是由这些RDD 所组成的序列(因此得名“离散化”)。
### 整体架构
![CleanShot 2024-04-09 at 12.45.47.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-09%20at%2012.45.47.png)

### 背压机制
为了协调数据接收速率与资源处理能力，SparkStreaming可以动态控制数据接收速率来适配集群数据处理能力。即背压机制（即 Spark Streaming Backpressure）: 根据JobScheduler 反馈作业的执行信息来动态调整Receiver 数据接收率。
>通过属性“spark.streaming.backpressure.enabled”来控制是否启用 backpressure 机制，默认值false，即不启用。

## DStream
### RDD队列
#### 用法及说明
测试过程中，可以通过使用 ssc.queueStream(queueOfRDDs)来创建DStream，每一个推送到这个队列中的RDD，都会作为一个DStream 处理。
#### 实操
```scala
val rddQueue = new mutable.Queue[RDD[Int]]()
//4.创建 QueueInputDStream
val inputStream = ssc.queueStream(rddQueue,oneAtATime = false)
//5.处理队列中的 RDD数据
val mappedStream = inputStream.map((_,1))
val reducedStream = mappedStream.reduceByKey(_ + _)
//6.打印结果
reducedStream.print()
```

### 自定义数据源
#### 用法及说明
需要继承Receiver，并实现 onStart、onStop 方法来自定义数据源采集。
#### 实操
```scala
class CustomerReceiver(host: String, port: Int) extends Receiver[String](StorageLevel.MEMORY_ONLY) {
	//最初启动的时候，调用该方法，作用为：读数据并将数据发送给 Spark
 	override def onStart(): Unit = {
 		new Thread("Socket Receiver") {
 			override def run() {
 				receive()
 			}
 		}.start()
 	}
	 //读数据并将数据发送给 Spark
	 def receive(): Unit = {
		 //创建一个 Socket
		 var socket: Socket = new Socket(host, port)
		 //定义一个变量，用来接收端口传过来的数据
		 var input: String = null
		 //创建一个 BufferedReader用于读取端口传来的数据
		 val reader = new BufferedReader(new InputStreamReader(socket.getInputStream, StandardCharsets.UTF_8))
	 	//读取数据
	 	input = reader.readLine()
	 	//当 receiver没有关闭并且输入数据不为空，则循环发送数据给 Spark
		while (!isStopped() && input != null) {
 			store(input)
 			input = reader.readLine()
 		}
 		//跳出循环则关闭资源
 		reader.close()
 		socket.close()
 		//重启任务
 		restart("restart")
 	}
 	override def onStop(): Unit = {}
 }
```

### Kafka数据源
>DirectAPI：是由计算的Executor 来主动消费Kafka 的数据，速度由自身控制。
>ReceiverAPI已经不适用了。

