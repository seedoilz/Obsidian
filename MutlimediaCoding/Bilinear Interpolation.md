---
aliases: 
tags:
  - code/coding
title: Bilinear Interpolation
date created: 2025-02-26 15:02:00
date modified: 2025-02-26 15:02:58
---
## Linear reconstruction - 1D
![CleanShot 2025-02-26 at 15.41.10.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-26%20at%2015.41.10.png)
## Bilinear Interpolation
> Linear reconstruction in 2D

When it comes 2D, we need to reconstruct pixel value from **4 neighbouring pixels**.
![CleanShot 2025-02-26 at 15.42.08.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-26%20at%2015.42.08.png)
Let first interpolate along x direction to get $𝐼(𝑥 + 𝑤_𝑥,𝑦)$ and $𝐼(𝑥 + 𝑤_𝑥, 𝑦 + 1)$ . This is simply 1D interpolation
![CleanShot 2025-02-26 at 15.44.01.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-26%20at%2015.44.01.png)


### Bicubic interpolation
![CleanShot 2025-02-26 at 15.44.41.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-26%20at%2015.44.41.png)
