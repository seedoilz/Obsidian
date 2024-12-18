---
aliases: 
tags:
  - code/algorithm
title: SVD
date created: 2024-12-07 19:12:00
date modified: 2024-12-17 15:12:75
---
SVD（Singular Value Decomposition，奇异值分解）是一种矩阵分解方法，它可以将一个矩阵分解成三个矩阵的乘积，用于很多应用，包括数据降维、信号处理和机器学习等。SVD对任意一个实数矩阵 AA（即不需要是方阵）都适用，公式为：
$$
A = U \Sigma V^T
$$
实际上可以如此理解，$U$为旋转，$\Sigma$为拉伸，而$V^T$也为旋转，A是一个线性变化。
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20241207194850.png)

对于M = U \Sigma V^T，M的横轴（行上的坐标信息）对应U，而M的列轴（列上的坐标信息）对应V^T

假设要计算某个坐标信息的坐标（例如要计算行上的坐标信息），那么就拿$U_k* \Sigma_k$，当然这里的U是指降维过后的选取前k列。

如果有一个新的行向量（对于M来说），那么映射到新的坐标公式为$r_{concept} = r * V_k * \Sigma_k^{-1}$（注意k）

如果有一个新的列向量（对于M来说），那么映射到新的坐标公式为$c_{concept} = \Sigma_k^{-1} * U_K^T * c$（注意k）