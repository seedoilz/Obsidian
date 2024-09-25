---
aliases: 
title: Flink状态后端
date created: 2024-09-21 21:09:00
date modified: 2024-09-21 21:09:81
---
>Flink 提供了 3 种状态后端用于管理和存储状态数据，我们来看看每种状态后端的适用场景：
## MemoryStateBackend
### 原理
运行时所需的 State 数据全部保存在 TaskManager JVM 堆上内存中，执行 Checkpoint 的时候，会把 State 的快照数据保存到 JobManager 进程 的内存中。执行 Savepoint 时，可以把 State 存储到文件系统中。
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240921215406.png)
### 适用场景
- 基于内存的 StateBackend 在生产环境下不建议使用，因为 State 大小超过 JobManager 内存就 OOM 了，此种状态后端适合在本地开发调试测试，生产环境基本不用。
- State 存储在 JobManager 的内存中。受限于 JobManager 的内存大小。
- 每个 State 默认 5MB，可通过 MemoryStateBackend 构造函数调整。
- 每个 Stale 不能超过 Akka Frame 大小。

## RocksDBStateBackend
### 原理
使用嵌入式的本地数据库 RocksDB 将流计算数据状态存储在本地磁盘中。在执行 Checkpoint 的时候，会将整个 RocksDB 中保存的 State 数据全量或者增量持久化到配置的文件系统中。
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240921215505.png)

### 适用场景
- 最适合用于处理大状态、长窗口，或大键值状态的有状态处理任务。
- RocksDBStateBackend 是目前唯一支持增量检查点的后端。
- 增量检查点非常适用于超大状态的场景。比如计算 DAU 这种大数据量去重，大状态的任务都建议直接使用 RocksDB 状态后端。


## FSStateBackend
### 原理
运行时所需的 State 数据全部保存在 TaskManager 的内存中，执行 Checkpoint 的时候，会把 State 的快照数据保存到配置的文件系统中。TM 是异步将 State 数据写入外部存储。
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240921215550.png)

### 适用场景
- 适用于处理小状态、短窗口、或者小键值状态的有状态处理任务，不建议在大状态的任务下使用 FSStateBackend。适用的场景比如明细层 ETL 任务，小时间间隔的 TUMBLE 窗口
- State 大小不能超过 TM 内存。