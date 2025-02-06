---
aliases:
tags: [code/coding]
title: Quantization
date created: 2025-02-06 20:02:00
date modified: 2025-02-06 20:02:88
---
## Scalar Quantization
### Definition
To represent a continuous scalar value fwith a finite number of bits, only a finite number of quantization levels Lcan be used. If each scalar is quantized independently, the procedure is called scalar quantization.
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250206200943.png)
### Uniform quantization
> 重建电平，决策边界
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250206201029.png)

#### Drawback
Not optimal. Sometimes, some codes will not be used.

### Non-uniform quantization
Optimally choose $r_i$ and $d_i$
Assume $f_{min} \le f \le f_{max}$
L = the number of reconstruction levels
$p_f(f_0)$: probability density function for f
Minimize $D = \mathbb{E} \left[ \left( \hat{f} - f \right)^2 \right] = \int_{f_0 = f_{\text{MIN}}}^{f_{\text{MAX}}} \left( \hat{f} - f \right)^2 p_f(f_0) \, df_0 \quad \hat{f} \in \{r_1, \dots, r_L\}$

## Vector Quantization
### Definition
- In Vector Quantization we call the set of reconstruction levels a codebook or dictionary and the space with each decision boundary a cell
- Using our intuition, the reconstruction levels could be the centers(or more properly centroids) of these cells

### How to find optimal decision boundaries
K-means Clustering

## Vector v.s. scalar quantization
Clearly VQ is computationally more intensive
- Requires training data
- Clustering takes time (for transmitter only)
- Receiver only needs to look up codebook