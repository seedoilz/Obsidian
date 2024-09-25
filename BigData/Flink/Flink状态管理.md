---
aliases: 
title: Flink状态管理
date created: 2024-09-21 21:09:00
date modified: 2024-09-21 21:09:31
tags: [code/big-data]
---
## Flink 中的状态
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240921213942.png)


- 由一个任务维护，通过[[Flink状态后端]]维护，并且用来计算某个结果的所有数据，都属于这个任务的状态 
- 可以认为状态就是一个本地变量，可以被任务的业务逻辑访问 
- Flink 会进行状态管理，包括状态一致性、故障处理以及高效存储和访问，以便开发人员可以专注于应用程序的逻辑

- 在 Flink 中，状态始终与特定算子相关联
- 为了使运行时的 Flink 了解算子的状态，算子需要预先注册其状态
- 总的说来，有两种类型的状态：
	- [[算子状态]]（Operator State）
		- 算子状态的作用范围限定为算子任务
	- [[键控状态]]（Keyed State）
		- 根据输入数据流中定义的键（key）来维护和访问

