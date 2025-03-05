---
aliases:
  - convolution
tags: 
title: 2D Filter
date created: 2025-02-19 18:02:00
date modified: 2025-02-26 15:02:82
---
## Definition
> Replace each pixel by a linear combination of its neighbors
> Only depends on relative position of neighbors
![CleanShot 2025-02-19 at 18.14.01.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-19%20at%2018.14.01.png)

### Padding
> To make sure input and output shares the same resolution, we extend the size of input

![CleanShot 2025-02-19 at 18.15.32.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-19%20at%2018.15.32.png)


### Many Different Filters
- Box Filter
- Gaussian kernel
- Sharpening kernel
- ......