---
aliases:
tags:
title: LTI System
date created: 2025-02-19 18:02:00
date modified: 2025-02-19 18:02:47
---

## LTI System = Linear and Time Invariant
Any LTI system can be characterized by its **impulse response**
![CleanShot 2025-02-19 at 18.20.35.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-19%20at%2018.20.35.png)

对于**LTI（线性时不变）系统**，**冲激响应完全决定了系统的行为**。如果我们知道冲激响应  h(t) ，就可以计算出系统对任何输入信号的响应。
根据**卷积定理**，对于任何输入信号  x(t) ，系统的输出  y(t)  可以通过**卷积**计算：
$$y(t) = x(t) * h(t)$$
即：
$$y[n] = \sum_{k=-\infty}^{\infty} x[k] h[n - k]$$


## 为什么卷积是LTI系统的输出

### 1. 系统的表示
一个系统 \( T \) 将输入 \( x(n_1, n_2) \) 与输出 \( y(n_1, n_2) \) 关联，表示为：
$$
y(n_1, n_2) = T[x(n_1, n_2)]
$$
其中，单位脉冲定义为：
$$
\delta(n_1, n_2) =
\begin{cases}
1, & n_1 = n_2 = 0 \\
0, & \text{否则}
\end{cases}
$$

### 2. 系统的线性性
一个系统是线性的，如果它满足：
$$
T[a_1x_1(n_1, n_2) + a_2x_2(n_1, n_2)] = a_1T[x_1(n_1, n_2)] + a_2T[x_2(n_1, n_2)]
$$
即，系统对输入信号的加权和，等于对各个输入信号分别处理后的加权和。

### 3. 系统的平移不变性
一个系统是平移不变的，如果它满足：
$$
T[x(n_1 - m_1, n_2 - m_2)] = y(n_1 - m_1, n_2 - m_2)
$$
即，系统对输入信号的平移操作，不会改变其输出信号的平移性质。

### 4. 线性性的推导
根据线性性，我们有：
$$
y(n_1, n_2) = T[x(n_1, n_2)] = T\left[\sum_{k_1 = -\infty}^{\infty} \sum_{k_2 = -\infty}^{\infty} x(k_1, k_2) \delta(n_1 - k_1, n_2 - k_2)\right]
$$
这可以重写为：
$$
y(n_1, n_2) = \sum_{k_1 = -\infty}^{\infty} \sum_{k_2 = -\infty}^{\infty} x(k_1, k_2) T[\delta(n_1 - k_1, n_2 - k_2)]
$$

### 5. 系统的冲激响应
如果系统的冲激响应是 \( h(n_1, n_2) = T[\delta(n_1, n_2)] \)，那么通过线性性可以得到：
$$
y(n_1, n_2) = \sum_{k_1 = -\infty}^{\infty} \sum_{k_2 = -\infty}^{\infty} x(k_1, k_2) h(n_1 - k_1, n_2 - k_2)
$$
这就是卷积操作。

### 6. 平移不变性的推导
根据平移不变性，我们有：
$$
h(n_1 - k_1, n_2 - k_2) = T[\delta(n_1 - k_1, n_2 - k_2)]
$$
所以，系统的输出是输入和冲激响应的卷积：
$$
y(n_1, n_2) = \sum_{k_1 = -\infty}^{\infty} \sum_{k_2 = -\infty}^{\infty} x(k_1, k_2) h(n_1 - k_1, n_2 - k_2) = x(n_1, n_2) * h(n_1, n_2)
$$

### 总结
- **线性性**：系统的响应是输入信号的加权和的响应的加权和。
- **平移不变性**：系统对输入信号的平移操作，其输出的平移性质不变。
- 因此，LTI系统的输出可以表示为输入信号和系统冲激响应的**卷积**。