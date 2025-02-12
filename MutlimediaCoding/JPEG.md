---
aliases: 
tags:
  - code/coding
title: JPEG
date created: 2025-02-08 15:02:00
date modified: 2025-02-10 09:02:92
---
1. RGB color model is transformed to CMY color model
2. DCT is applied on every 8x8 CMY color model
	1. Intensity level is more important than color channels
3. Threshold coding is applied on the results of DCT and quantization matrix
4. Differential Pulse Code Modulation (DPCM) is applied on the DC component
	1. Encodes the differences between the current and previous 8x8 block
5. RLE is applied on the 63 AC components
6. Huffman coding is applied on the results from DPCM coding and RLE coding
	1. There are at least 3 code tables (DC, run-length, level)