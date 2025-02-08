---
aliases: 
tags:
  - code/coding
title: Video Encoding
date created: 2025-02-08 15:02:00
date modified: 2025-02-08 15:02:13
---
## Video coding: A simple solution
- Video is a sequence of images
- So the simple way is encode each frame independently
	- This is called: Motion JPEG
	- Actually many movie industry uses it

## Motion Estimation
- Assume objects or parts of the objects in the scene has moved (translated)
	- Given short time between frames, also act as a rough estimation of scaling and rotation

## MPEG-1
- P-frame based on previous I-frame or P-frame
- B-frame based on I-frame or P-frame around
- No one depends on B-picture
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208152338.png)
### Rationale
- If we want to reduce file size, we can reduce quality of B-pictures without causing problem
	- I-picture and P-picture, on the other hand, has to remain high-enough quality
- Fast-forward and rewinding to I-pictures only
- Chroma subsampling

## MPEG-2
- Support for **interlaced** video
- Interlacing:a technique for doubling the perceived framerate of a video without extra bandwidth (wikipedia)
- Fields:odd/even lines of a frame
- Each time, odd fields are displayed first, then even fields are filled up, then odd fields…
	- Effectively doubles the frame-rate without additional frames

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208152736.png)
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208152802.png)
## MPEG-4
> Different video objects (VO) are encoded and decoded individually
> May use different encoding methods as they serve different purposes

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208152909.png)
