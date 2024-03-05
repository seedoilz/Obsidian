---
aliases:
  - Dependecy Injection
title: DI
date created: 三月 5日 2024, 4:30:40 下午
date modified: 三月 5日 2024, 4:39:06 下午
tags:
  - code/web
---
### 概念
指Spring创建对象的过程中，将对象依赖属性通过配置进行注入

#### 实现方式
- 第一种：set注入
- 第二种：构造注入
所以结论是：IOC 就是一种控制反转的思想， 而 DI 是对IoC的一种具体实现。

Bean管理说的是：Bean对象的创建，以及Bean对象中属性的赋值（或者叫做Bean对象之间关系的维护）。