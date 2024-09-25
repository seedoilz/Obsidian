---
aliases: 
title: Flink CEP
date created: 2024-09-22 13:09:00
date modified: 2024-09-22 14:09:19
tags: [code/big-data]
---

## CEP
- 目标：从有序的简单事件流中发现一些高阶特征
- 输入：一个或多个由简单事件构成的事件流
- 处理：识别简单事件之间的内在联系，多个符合一定规则的简单事件构成复杂事件
- 输出：满足规则的复杂事件

### Pattern API
处理事件的规则，被叫做“模式”（Pattern）
Flink CEP 提供了 Pattern API，用于对输入流数据进行复杂事件规则定义，用来提取符合规则的事件序列
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240922141610.png)

#### 种类
##### 个体模式（Individual Patterns）
>组成复杂规则的每一个单独的模式定义，就是“个体模式”

个体模式可以包括“单例（singleton）模式”和“循环（looping）模式”
单例模式只接收一个事件，而循环模式可以接收多个

###### 条件（Condition）
每个模式都需要指定触发条件，作为模式是否接受事件进入的判断依据
CEP 中的个体模式主要通过调用 .where() .or() 和 .until() 来指定条件
按不同的调用方式，可以分成以下几类：
- 简单条件（Simple Condition）
	- 通过 .where() 方法对事件中的字段进行判断筛选，决定是否接受该事件
	- ![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240922143047.png)
- 组合条件（Combining Condition）
	- 将简单条件进行合并；.or() 方法表示或逻辑相连，where 的直接组合就是 AND
```java
pattern.where(event => ...).or(event => ...)
```
- 终止条件（Stop Condition）
	- 如果使用了 oneOrMore 或者 oneOrMore.optional，建议使用 .until() 作为终止条件，以便清理状态
- 迭代条件（Iterative Condition）
	- 能够对模式之前所有接收的事件进行处理
	- 可以调用 ctx.getEventsForPattern(“name”)
```java
.where(new IterativeCondition<Event>(){...})
```
```java
start.times(3).where(new SimpleCondition<Event>(){...})
```

##### 组合模式（Combining Patterns，也叫模式序列）
很多个体模式组合起来，就形成了整个的模式序列
模式序列必须以一个“初始模式”开始：
```java
Pattern<Event, Event> start = pattern.<Event>begin("start")
```

##### 模式组（Groups of patterns）
将一个模式序列作为条件嵌套在个体模式里，成为一组模式

#### 模式的检测
指定要查找的模式序列后，就可以将其应用于输入流以检测潜在匹配
调用 CEP.pattern()，给定输入流和模式，就能得到一个PatternStream
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240922143236.png)

#### 匹配事件的提取
创建 PatternStream 之后，就可以应用 select 或者 flatselect 方法，从检测到的事件序列中提取事件了

select() 方法需要输入一个 select function 作为参数，每个成功匹配的事件序列都会调用它

select() 以一个 `Map<String，List <IN>>` 来接收匹配到的事件序列，其中 key 就是每个模式的名称，而 value 就是所有接收到的事件的 List 类型
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240922143328.png)
