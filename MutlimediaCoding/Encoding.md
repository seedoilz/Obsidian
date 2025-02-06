---
aliases: 
tags:
  - code/coding
title: Encoding
date created: 2025-02-06 20:02:00
date modified: 2025-02-06 20:02:19
---
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250206200733.png)

1. Transformer: transform the input data into a form more amenable to compression. E.g. Discrete Fourier Transforms (DFT), Discrete Cosine Transform (DCT) etc.
2. Quantizer([[Quantization]]): represent transformed signal with a limited number of levels/symbols; an irreversible operation; E.g. scalar quantization, vector quantization
3. Codeword Assignment: Assign codewords to the quantized output, creating a bit stream; E.g. fixed length coding v.s variable length coding