---
aliases: 
tags:
  - code/coding
title: Multiple-symbols encoding
date created: 2025-02-06 20:02:00
date modified: 2025-02-08 14:02:10
---
Two types:
- Static dictionary
- Adaptive dictionary

## Static dictionary
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250206205236.png)

## Adaptive dictionary
### LZ77
LZ77的核心思想是通过查找重复的字符串片段来压缩数据，具体操作是将数据中的重复部分以引用的形式表示，而不是存储实际的重复内容。这个引用包括三个部分：
1. **距离（Distance）**：表示当前字符串在数据流中距离前一个出现位置的距离。
2. **长度（Length）**：表示当前匹配字符串的长度。
3. **匹配后紧接着的字符（The symbol following the match）**

LZ77通过滑动窗口（sliding window）技术来查找数据流中重复的字符串。窗口分为两部分：
- **搜索窗口（Search window）**：存储已处理的数据部分（通常是固定大小）。
- **前瞻窗口（Lookahead buffer）**：存储当前正在压缩的数据部分。

（其中，距离加长度可以超过Lookahead的头部）

#### Example
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208135851.png)
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250208135907.png)
### LZ78
#### 输出形式
LZ78 encodes an input sequences as pairs <i, c>:
- i: An index corresponding to the dictionary entry that is the longest match to the input. It is 0 in case of no match
- c: The code for the character after the match portion of the input

#### 编码过程
- **初始化一个空字典**。
- **从输入数据流读取字符，并逐步构建最长匹配的字符串**：
    - 如果当前字符串**已经存在**于字典中，则继续读取下一个字符。
    - 如果当前字符串**不存在**于字典中，则：
        - 将其**添加到字典**中，并分配一个索引编号。
        - 输出**（匹配的索引，新字符）** 作为编码结果。
- **重复上述步骤，直到所有数据处理完毕**。