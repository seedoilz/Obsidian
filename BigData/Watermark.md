---
aliases: 
title: Watermark
date created: 2024-04-26 11:04:00
date modified: 2024-04-26 11:04:60
tags: [code/big-data]
---
## 基本概念
### 使用原因
流处理从事件产生，到流经 source，再到 operator，虽然大部分情况下，流到 operator 的数据都是按照事件产生的时间顺序来的，但是由于网络、分布式等原因，导致乱序的产生。所谓乱序，就是指 Flink 接收到的事件的先后顺序不是严格按照事件的 Event Time 顺序排列的。
一旦出现乱序，如果只根据 eventTime 决定 window 的运行，我们不能明确数据是否全部到位，但又不能无限期的等下去，此时必须要有个机制来保证一个特定的时间后，必须触发 window 去进行计算了，这个特别的机制，就是 Watermark。
### 概念
- Watermark 是一种衡量 Event Time 进展的机制。
- Watermark 是用于处理乱序事件的，而正确的处理乱序事件，通常用 Watermark 机制结合 window 来实现。
- 数据流中的 Watermark 用于表示 timestamp 小于 Watermark 的数据，都已经到达了，因此，window 的执行也是由 Watermark 触发的。
- Watermark 可以理解成一个延迟触发机制，我们可以设置 Watermark 的延时时长 t，每次系统会校验已经到达的数据中最大的 maxEventTime，然后认定 eventTime 小于 maxEventTime - t 的所有数据都已经到达，如果有窗口的停止时间等于 maxEventTime – t，那么这个窗口被触发执行。
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/2024-04-26-11-57-34.png)
