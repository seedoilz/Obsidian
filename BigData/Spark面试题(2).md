---
aliases: 
title: Spark面试题(2)
date created: 2024-04-13 19:04:00
date modified: 2024-04-13 19:04:74
tags:
  - code/big-data
---
## Spark 面试题（二）

### 1、Spark 有哪两种算子？

Transformation（转化）算子和 Action（执行）算子。

### 2、Spark 有哪些聚合类的算子, 我们应该尽量避免什么类型的算子？

在我们的开发过程中，能避免则尽可能避免使用 reduceByKey、join、distinct、repartition 等会进行 shuffle 的算子，尽量使用 map 类的非 shuffle 算子。 
这样的话，没有 shuffle 操作或者仅有较少 shuffle 操作的 Spark 作业，可以大大减少性能开销。

### 3、如何从 Kafka 中获取数据？

1）基于 Receiver 的方式 
这种方式使用 Receiver 来获取数据。Receiver 是使用 Kafka 的高层次 Consumer API 来实现的。receiver 从 Kafka 中获取的数据都是存储在 Spark Executor 的内存 
中的，然后 Spark Streaming 启动的 job 会去处理那些数据。 
2）基于 Direct 的方式 
这种新的不基于 Receiver 的直接方式，是在 Spark 1.3中引入的，从而能够确保更加健壮的机制。替代掉使用 Receiver 来接收数据后，这种方式会周期性地 
查询 Kafka，来获得每个 topic+partition 的最新的 offset，从而定义每个 batch 的 offset 的范围。当处理数据的 job 启动时，就会使用 Kafka 的简单 consumer api 来 
获取 Kafka 指定 offset 范围的数据。

### 4、RDD 创建有哪几种方式？

1）使用程序中的集合创建 rdd 
2）使用本地文件系统创建 rdd 
3）使用 hdfs 创建 rdd 
4）基于数据库 db 创建 rdd 
5）基于 Nosql 创建 rdd，如 hbase 
6）基于 s3创建 rdd 
7）基于数据流，如 socket 创建 rdd

### 5、Spark 并行度怎么设置比较合适？

spark 并行度，每个 core 承载2~4个 partition, 如，32个 core，那么64~128之间的并行度，也就是设置64~128个 partion，并行读和数据规模无关， 
只和内存使用量和 cpu 使用时间有关。

### 6、Spark 如何处理不能被序列化的对象？

将不能序列化的内容封装成 object。

### 7、collect 功能是什么，其底层是怎么实现的？

driver 通过 collect 把集群中各个节点的内容收集过来汇总成结果，collect 返回结果是 Array 类型的，collect 把各个节点上的数据抓过来， 
抓过来数据是 Array 型，collect 对 Array 抓过来的结果进行合并，合并后 Array 中只有一个元素，是 tuple 类型（KV 类型的）的。

### 8、为什么 Spark Application 在没有获得足够的资源，job 就开始执行了，可能会导致什么什么问题发生？

会导致执行该 job 时候集群资源不足，导致执行 job 结束也没有分配足够的资源，分配了部分 Executor，该 job 就开始执行 task，应该是 task 的调度线程 
和 Executor 资源申请是异步的；如果想等待申请完所有的资源再执行 job 的： 
需要将 
spark. scheduler. maxRegisteredResourcesWaitingTime 设置的很大； 
spark. scheduler. minRegisteredResourcesRatio 设置为1，但是应该结合实际考虑 
否则很容易出现长时间分配不到资源，job 一直不能运行的情况。

### 9、map 与 flatMap 的区别？

map：对 RDD 每个元素转换，文件中的每一行数据返回一个数组对象。 
flatMap：对 RDD 每个元素转换，然后再扁平化。 
将所有的对象合并为一个对象，文件中的所有行数据仅返回一个数组对象，会抛弃值为 null 的值。

### 10、Spark on Mesos 中，什么是的粗粒度分配，什么是细粒度分配，各自的优点和缺点是什么？

1）粗粒度：启动时就分配好资源，程序启动，后续具体使用就使用分配好的资源，不需要再分配资源；优点：作业特别多时，资源复用率高，适合粗粒度； 
缺点：容易资源浪费，假如一个 job 有1000个 task，完成了999个，还有一个没完成，那么使用粗粒度，999个资源就会闲置在那里，资源浪费。 
2）细粒度分配：用资源的时候分配，用完了就立即回收资源，启动会麻烦一点，启动一次分配一次，会比较麻烦。

### 11、driver 的功能是什么？

1）一个 Spark 作业运行时包括一个 Driver 进程，也是作业的主进程，具有 main 函数，并且有 SparkContext 的实例，是程序的入口点； 
2）功能：负责向集群申请资源，向 master 注册信息，负责了作业的调度，负责作业的解析、生成 Stage 并调度 Task 到 Executor 上。包括 DAGScheduler， TaskScheduler。

### 12、Spark 技术栈有哪些组件，每个组件都有什么功能，适合什么应用场景？

可以画一个这样的技术栈图先，然后分别解释下每个组件的功能和场景 
1）Spark core：是其它组件的基础，spark 的内核，主要包含：有向循环图、RDD、Lingage、Cache、broadcast 等，并封装了底层通讯框架， 
是 Spark 的基础。 
2）SparkStreaming 是一个对实时数据流进行高通量、容错处理的流式处理系统，可以对多种数据源（如Kafka、Flume、Twitter、Zero和TCP 套接字） 
进行类似 Map、Reduce 和 Join 等复杂操作，将流式计算分解成一系列短小的批处理作业。 
3）Spark sql：Shark 是 SparkSQL 的前身，Spark SQL 的一个重要特点是其能够统一处理关系表和 RDD，使得开发人员可以轻松地使用 SQL 命令进行外部查询， 
同时进行更复杂的数据分析。 
4）BlinkDB ：是一个用于在海量数据上运行交互式 SQL 查询的大规模并行查询引擎，它允许用户通过权衡数据精度来提升查询响应时间，其数据的精度 
被控制在允许的误差范围内。 
5）MLBase 是 Spark 生态圈的一部分专注于机器学习，让机器学习的门槛更低，让一些可能并不了解机器学习的用户也能方便地使用 MLbase。 
MLBase 分为四部分：MLlib、MLI、ML Optimizer 和 MLRuntime。 
6）GraphX 是 Spark 中用于图和图并行计算。

### 13、Spark 中 Worker 的主要工作是什么？

主要功能：管理当前节点内存，CPU 的使用状况，接收 master 分配过来的资源指令，通过 ExecutorRunner 启动程序分配任务，worker 就类似于包工头， 
管理分配新进程，做计算的服务，相当于 process 服务。 
需要注意的是： 
1）worker 会不会汇报当前信息给 master，worker 心跳给 master 主要只有 workid，它不会发送资源信息以心跳的方式给 mater，master 分配的时候就知道 work， 
只有出现故障的时候才会发送资源。 
2）worker 不会运行代码，具体运行的是 Executor 是可以运行具体 appliaction 写的业务逻辑代码，操作代码的节点，它不会运行程序的代码的。

### 14、Mapreduce 和 Spark 的都是并行计算，那么他们有什么相同和区别？

两者都是用 mr 模型来进行并行计算: 
1）hadoop 的一个作业称为 job，job 里面分为 map task 和 reduce task，每个 task 都是在自己的进程中运行的，当 task 结束时，进程也会结束。 
2）spark 用户提交的任务成为 application，一个 application 对应一个 SparkContext，app 中存在多个 job，每触发一次 action 操作就会产生一个 job。 
这些 job 可以并行或串行执行，每个 job 中有多个 stage，stage 是 shuffle 过程中 DAGSchaduler 通过 RDD 之间的依赖关系划分 job 而来的，每个 stage 里面有多个 task， 
组成 taskset 有 TaskSchaduler 分发到各个 executor 中执行，executor 的生命周期是和 app 一样的，即使没有 job 运行也是存在的，所以 task 可以快速启动读取内存 
进行计算。 
3）hadoop 的 job 只有 map 和 reduce 操作，表达能力比较欠缺而且在 mr 过程中会重复的读写 hdfs，造成大量的 io 操作，多个 job 需要自己管理关系。 
4）spark 的迭代计算都是在内存中进行的，API 中提供了大量的 RDD 操作如 join，groupby 等，而且通过 DAG 图可以实现良好的容错。

### 15、RDD 机制？

rdd 分布式弹性数据集，简单的理解成一种数据结构，是 spark 框架上的通用货币。所有算子都是基于 rdd 来执行的，不同的场景会有不同的 rdd 实现类， 
但是都可以进行互相转换。rdd 执行过程中会形成 dag 图，然后形成 lineage 保证容错性等。从物理的角度来看 rdd 存储的是 block 和 node 之间的映射。

### 16、什么是 RDD 宽依赖和窄依赖？

RDD 和它依赖的 parent RDD (s) 的关系有两种不同的类型，即窄依赖（narrow dependency）和宽依赖（wide dependency） 
1）窄依赖指的是每一个 parent RDD 的 Partition 最多被子 RDD 的一个 Partition 使用 
2）宽依赖指的是多个子 RDD 的 Partition 会依赖同一个 parent RDD 的 Partition

### 17、cache 和 pesist 的区别？

cache 和 persist 都是用于将一个 RDD 进行缓存的，这样在之后使用的过程中就不需要重新计算了，可以大大节省程序运行时间 
1） cache 只有一个默认的缓存级别 MEMORY_ONLY ，cache 调用了 persist，而 persist 可以根据情况设置其它的缓存级别； 
2）executor 执行的时候，默认60%做 cache，40%做 task 操作，persist 是最根本的函数，最底层的函数。

### 18、 cache 后面能不能接其他算子, 它是不是 action 操作？

cache 可以接其他算子，但是接了算子之后，起不到缓存应有的效果，因为会重新触发 cache。 
cache 不是 action 操作。

### 19、reduceByKey 是不是 action？

不是，很多人都会以为是 action，reduce rdd 是 action

### 20、 RDD 通过 Linage（记录数据更新）的方式为何很高效？

1）lazy 记录了数据的来源，RDD 是不可变的，且是 lazy 级别的，且 RDD 之间构成了链条，lazy 是弹性的基石。由于 RDD 不可变，所以每次操作就产生新的 rdd， 
不存在全局修改的问题，控制难度下降，所有有计算链条将复杂计算链条存储下来，计算的时候从后往前回溯 900步是上一个 stage 的结束，要么就 checkpoint。 
2）记录原数据，是每次修改都记录，代价很大如果修改一个集合，代价就很小，官方说 rdd 是粗粒度的操作，是为了效率，为了简化，每次都是操作数据集合， 
写或者修改操作，都是基于集合的 rdd 的写操作是粗粒度的，rdd 的读操作既可以是粗粒度的也可以是细粒度，读可以读其中的一条条的记录。 
3）简化复杂度，是高效率的一方面，写的粗粒度限制了使用场景如网络爬虫，现实世界中，大多数写是粗粒度的场景。