---
aliases: 
tags:
  - input
title: Point operation
date created: 2025-02-12 14:02:00
date modified: 2025-02-12 15:02:22
---
# Point operation
> Just consider one pixel at a time

## Digital gain
### point-wise multiplication
One simple operation is to multiple point-wise constant
$$
I_o = a \cdot I_i
$$

### clipping
Normally, most of image format cannot handle out-of-range value, so we apply a clipping is applied after processing:
- For uint8 image, we clip to \[0, 255\]
- For floating-point image, we clip to \[0.0, 1.0\]

![CleanShot 2025-02-12 at 14.50.00.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2014.50.00.png)

### Curve
For point operation, since both input and output are single-value pixel, we basically define a 1D function from input to output
- This function can be visualize using 1D curve
- This curve may have different names depends on the use case:
	- Tone curve
	- Color curve
	- Gamma curve
#### curve of digital gain
![CleanShot 2025-02-12 at 14.56.56.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2014.56.56.png)

### Inversion
![CleanShot 2025-02-12 at 15.02.49.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2015.02.49.png)

### Drawbacks of a digital gain
1. Darkening (digital gain < 1.0)
	1. Part of image range is not utilized
	2. This is a waste of image dynamic range
2. Brightening (digital gain > 1.0)
	1. Highlight clipping

![CleanShot 2025-02-12 at 15.04.28.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2015.04.28.png)
# Gamma curve and manual curve
## Requirements
- Utilize the full dynamic range
	- 0.0 -> 0.0
	- 1.0 -> 1.0
- Shadow
- Apply certain gains to brighten/darken shadow region
- Highlight
	- No clipping

![CleanShot 2025-02-12 at 15.07.49.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2015.07.49.png)


# Histogram
> number of pixels as a function of image intensity
![CleanShot 2025-02-12 at 15.39.26.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2015.39.26.png)
![CleanShot 2025-02-12 at 15.58.20.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202025-02-12%20at%2015.58.20.png)
