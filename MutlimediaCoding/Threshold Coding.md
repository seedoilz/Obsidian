---
aliases: 
tags:
  - code/coding
title: Threshold Coding
date created: 2025-02-08 14:02:00
date modified: 2025-02-08 15:02:31
---
## Zonal Coding
- Assumption: low-frequency component contains more information
- Calculate variances for each transform coefficients across each image block
- Zonal coding masks crop insignificant coefficients
- Zonal bit allocationfurther assigns more bits to coefficients with higher variances
- In practice, we only apply DCT to each 8 × 8 block, instead of the entire image
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208145611.png)
## Assumption by Zonal Coding
Assumption: low-frequency component contains more information => Is that always true?

Low-frequency in general, has a higher possibility to contain more information, but some high-frequency component may contain large information.

## Solution: Threshold Coding
> Magnitude must exceed a certain threshold to be retained (retained coefficients thus different from block to block)

### Several options for threshold coding
- Global threshold -same threshold for all coefficients; if lower than the global threshold, output is 0
- Highest-N -consider all coefficients, retain only the highest N ones
- Normalization -all coefficients are normalized by some normalization matrix before thresholding
	- It is normalized using a quantization matrix based on human visual system's response to luminance and chrominance

#### How to encode with most efficiency?
##### Zig-zag scanning
> Starting from top-left corner and scan in a zig-zag manner
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208150547.png)

##### Run-length Encoding
> Run-length is the amount of zero encountered before a non-zero coefficient

- Instead of encoding each coefficient one by one, we encoding(run-length, level) pairs where level is the first non-zero coefficient after the zeroes
- Combined with threshold coding it's usually(run-length, size, level) where size is the corresponding value in quantization matrix and level is the quantized coefficient
- E.g. (5, 4, 3) would mean 5 zeroes, followed by a size-4 quantization with output quantization level of 3