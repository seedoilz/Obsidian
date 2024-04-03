---
aliases: 
title: Yarn
date created: 2024-03-12 13:03:00
date modified: 2024-04-03 19:04:56
tags:
  - code/big-data
  - input
---
## [[Yarn概述]]
### 组成
1. ResourceManager（RM）：整个集群资源（内存、CPU等）的老大
2. NodeManager（NM）：单个节点服务器资源老大
3. ApplicationMaster（AM）：单个任务运行的老大
4. Container：容器，相当一台独立的服务器，里面封装了任务运行所需要的资源，如内存、CPU、磁盘、网络等。
![CleanShot 2024-03-31 at 20.13.18.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2020.13.18.png)

### 工作机制

![CleanShot 2024-03-31 at 20.23.44.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-03-31%20at%2020.23.44.png)
> [!NOTE] 解释
> 1. MapReduce 程序提交到客户端所在的节点。
> 2. YarnRunner 向 ResourceManager 申请一个Application。
> 3. ResourceManager 将该**应用程序的资源路径（存放位置）**返回给YarnRunner。
> 4. 该程序将运行所需资源提交到HDFS 上。
> 5. 程序资源提交完毕后，申请运行 mrAppMaster。
> 6. ResourceManager 将用户的请求初始化成一个 Task。
> 7. 其中一个NodeManager 领取到 Task 任务。
> 8. 该 NodeManager 创建容器Container，并产生 MRAppmaster。
> 9. Container 从HDFS 上拷贝资源到本地。
> 10. MRAppmaster 向RM 申请运行 MapTask 资源。
> 11. RM 将运行 MapTask 任务分配给另外两个 NodeManager， 另两个 NodeManager 分别领取任务并创建容器。
> 12. MR 向两个接收到任务的NodeManager 发送程序启动脚本，这两个 NodeManager 分别启动MapTask，MapTask 对数据分区排序。
> 13. MrAppMaster 等待所有MapTask 运行完毕后，向RM 申请容器， 运行ReduceTask。
> 14. ReduceTask 向MapTask 获取相应分区的数据。
> 15. 程序运行完毕后，MR 会向 ResourceManager 申请注销自己。

## [[Yarn调度]]
>Hadoop 作业调度器主要有三种：FIFO、容量（Capacity Scheduler）和公平（Fair Scheduler）。Apache Hadoop3.1.3 默认的资源调度器是 Capacity Scheduler。

### 先进先出调度器（FIFO）
优点：简单易懂；
缺点：不支持多队列，生产环境很少使用；
![CleanShot 2024-04-01 at 20.18.39.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-01%20at%2020.18.39.png)

### 容量调度器（Capacity Scheduler）
![CleanShot 2024-04-01 at 20.21.35.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-01%20at%2020.21.35.png)
1. 多队列：每个队列可配置一定的资源量，每个队列采用FIFO调度策略。
2. 容量保证：管理员可为每个队列设置资源最低保证和资源使用上限
3. 灵活性：如果一个队列中的资源有剩余，可以暂时共享给那些需要资源的队列，而一旦该队列有新的应用程序提交，则其他队列借调的资源会归还给该队列。
4. 多租户：
	1. 支持多用户共享集群和多应用程序同时运行。
	2. 为了防止同一个用户的作业独占队列中的资源，该调度器会对同一用户提交的作业所占资源量进行限定。

#### 容量调度器资源分配算法
1. 队列资源分配
	1. 从root开始，使用深度优先算法，优先选择资源占用率最低的队列分配资源。
2. 作业资源分配
	1. 默认按照提交作业的优先级和提交时间顺序分配资源。
3. 容器资源分配
	1. 按照容器的优先级分配资源；
	2. 如果优先级相同，按照数据本地性原则：
		1. 任务和数据在同一节点
		2. 任务和数据在同一机架
		3. 任务和数据不在同一节点也不在同一机架

### 公平调度器（Fair Scheduler）
![CleanShot 2024-04-01 at 20.32.11.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-01%20at%2020.32.11.png)

#### 与容量调度对比
##### 相同点
1. 多队列：每个队列可配置一定的资源量，每个队列采用FIFO调度策略。
2. 容量保证：管理员可为每个队列设置资源最低保证和资源使用上限
3. 灵活性：如果一个队列中的资源有剩余，可以暂时共享给那些需要资源的队列，而一旦该队列有新的应用程序提交，则其他队列借调的资源会归还给该队列。
4. 多租户：
	1. 支持多用户共享集群和多应用程序同时运行。
	2. 为了防止同一个用户的作业独占队列中的资源，该调度器会对同一用户提交的作业所占资源量进行限定。

##### 不同点
1. 核心调度策略不同
	1. 容量调度器：优先选择资源**利用率**低的队列
	2. 公平调度器：优先选择对资源的**缺额**比例大的
2. 每个队列可以单独设置资源分配方式
	1. 容量调度器：FIFO、 DRF
	2. 公平调度器：FIFO、FAIR、DRF

##### 缺额是什么？
- 公平调度器设计目标是：在时间尺度上，所有作业获得公平的资源。某一时刻一个作业应获资源和实际获取资源的差距叫“缺额”
- 调度器会优先为缺额大的作业分配资源
![CleanShot 2024-04-01 at 20.37.28.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-01%20at%2020.37.28.png)

##### 公平调度器队列资源分配方式
###### FIFO策略
公平调度器每个队列资源分配策略如果选择FIFO的话，此时公平调度器相当于上面讲过的容量调度器。
###### Fair策略
Fair 策略（默认）是一种基于最大最小公平算法实现的资源多路复用方式，默认情况下，每个队列内部采用该方式分配资源。这意味着，如果一个队列中有两个应用程序同时运行，则每个应用程序可得到1/2的资源；如果三个应用程序同时运行，则每个应用程序可得到1/3的资源。
具体资源分配流程和容量调度器一致；
（1）选择队列
（2）选择作业
（3）选择容器
以上三步，每一步都是按照公平策略分配资源
![CleanShot 2024-04-01 at 21.11.32.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-04-01%20at%2021.11.32.png)
> [!NOTE] 解释
实际最小资源份额：mindshare = Min（资源需求量，配置的最小资源）
是否饥饿：isNeedy = 资源使用量< mindshare（实际最小资源份额）
资源分配比：minShareRatio = 资源使用量/ Max（mindshare, 1）
资源使用权重比：useToWeightRatio = 资源使用量/ 权重