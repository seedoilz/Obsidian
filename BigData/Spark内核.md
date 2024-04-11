---
aliases: 
title: Spark内核
date created: 2024-04-11 14:04:00
date modified: 2024-04-11 15:04:50
tags: [code/big-data]
---
## [[Spark运行机制]]
### 通用运行流程
1. 任务提交后，都会先启动 Driver 程序；
2. 随后 Driver 向集群管理器注册应用程序；
3. 之后集群管理器根据此任务的配置文件分配 Executor 并启动；
4. Driver 开始执行 main 函数，Spark 查询为懒执行，当执行到 Action 算子时开始反向推算，根据宽依赖进行 Stage 的划分，随后每一个 Stage 对应一个 Taskset，Taskset 中有多个 Task，查找可用资源 Executor 进行调度；
5. 根据本地化原则，Task 会被分发到指定的 Executor 去执行，在任务执行的过程中，Executor 也会不断与 Driver 进行通信，报告任务运行情况。

### YARN Cluster 模式
1. 执行脚本提交方法，实际启动了一个 SparkSubmit 的 JVM 进程；
2. SparkSubmit 调用了 YarnClusterApplication 的 main 方法
3. YarnClusterApplication 创建了 Yarn 客户端，然后向 Yarn 服务器发送执行启动 ApplicationMaster 的指令。
4. Yarn 框架收到指令后会在指定的 NodeManager 中启动 ApplicationMaster
5. ApplicationMaster 启动 Driver 线程，执行用户的作业
6. AM 向 RM 注册，申请资源启动 NM；
7. 获取资源后 AM 向 NM 发送指令: `bin/java YarnCoarseGrainedExecutorBackend`
8. CoarseGrainedExecutorBackend 进程会接受消息，跟 Driver 通信，注册已经启动的 Executor；然后启动计算对象 Executor 等待接收任务。
9. Driver 线程继续执行完成作业的调度和任务的执行。
10. Driver 分配任务并监控任务的执行。
![截屏2024-04-11 下午3.22.16.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-15-22-20.png)

### YARN Client 模式
1. 执行脚本提交任务，实际是启动一个 SparkSubmit 的 JVM 进程；
2. SparkSubmit 类中的 main 方法反射调用用户代码的 main 方法；
3. 启动 Driver 线程，执行用户的作业，并创建 ScheduleBackend；
4. YarnClientSchedulerBackend 向 RM 发送指令：bin/java ExecutorLauncher；
5. Yarn 框架收到指令后会在指定的 NM 中启动 ExecutorLauncher（实际上还是调用 ApplicationMaster 的 main 方法）
6. AM 向 RM 注册，申请资源
7. 获取资源后 AM 向 NM 发送指令：bin/java CoarseGrainedExecutorBackend；
8. CoarseGrainedExecutorBackend 进程会接收消息，跟 Driver 通信，注册已经启动的 Executor；然后启动计算对象 Executor 等待接收任务
9. Driver 分配任务并监控任务的执行。
![截屏2024-04-11 下午3.36.41.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-15-37-58.png)

### Standalone Client 模式
>在 Standalone Client 模式下，Driver 在任务提交的本地机器上运行。
![截屏2024-04-11 下午3.43.40.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-15-43-44.png)
### Standalone Cluster 模式
>在 Standalone Cluster 模式下，任务提交后，Master 会找到一个 Worker 启动 Driver。
![截屏2024-04-11 下午3.43.59.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-15-44-20.png)

## [[Spark通讯架构]]
### 通讯架构概述
Spark2.x 版本使用 Netty 通讯框架作为内部通讯组件，借鉴了Akka的设计，基于Actor模型。
Spark通讯框架中各个组件被认为是一个个独立的实体，各个实体之间通过消息来进行通信。
![截屏2024-04-11 下午3.53.39.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-15-53-49.png)

Endpoint （Client/Master/Worker）有**1 个InBox 和N 个OutBox** （N>=1， N 取决于当前 Endpoint与多少其他的Endpoint 进行通信，一个与其通讯的其他Endpoint 对应一个OutBox）， Endpoint接收到的消息被写入InBox，发送出去的消息写入OutBox 并被发送到其他Endpoint 的InBox中。
### 通讯架构解析
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-11-16-08-44.png)

## Spark任务调度机制




