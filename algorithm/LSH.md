---
aliases:
  - Locality-sensitive hashing
tags:
  - code/algorithm
title: LSH
date created: 2024-12-07 15:12:00
date modified: 2024-12-17 21:12:29
---
## 局部敏感哈希（LSH）流程

### 1. 背景与目标
- 目标：在高维数据集中快速找到**相似项**（如文档、用户、图像）。
- 挑战：直接计算所有数据对的相似性代价高昂（时间复杂度为 \( O(n^2) \)）。
- 解决方案：利用 LSH 降低计算量，近似找到相似数据。

### 2. 流程步骤

#### Step 1: Shingling（文档预处理）
- **目的**：将文档转化为集合形式，捕捉文本的顺序信息。
- **方法**：
  1. 将文档分成 \( k \)-shingle（即长度为 \( k \) 的字符串片段）。
     - 例如，文档 `abcab`，当 \( k = 2 \) 时，k-shingle 集合为 `{ab, bc, ca}`。
  2. 对每个文档生成其 \( k \)-shingle 集合。

#### Step 2: MinHashing（生成签名）
- **目的**：将大的集合表示为固定长度的签名，同时保留相似性。
- **方法**：
  1. 设计多个随机哈希函数 \( h_1, h_2, ..., h_k \)。
  2. 对于每个文档集合，计算所有元素在每个哈希函数下的最小哈希值（MinHash）。
     - MinHash 的数学依据：
       $$
       P[\text{MinHash}(A) = \text{MinHash}(B)] = \text{Jaccard Similarity}(A, B)
       $$
  3. 结果：每个文档用一个长度为 \( k \) 的签名向量表示。

#### Step 3: Locality-Sensitive Hashing（候选对筛选）
- **目的**：快速找出“可能相似”的文档对（候选对），避免全对全比较。
- **方法**：
  1. **划分签名矩阵**：
     - 将 MinHash 签名矩阵按行划分为 \( b \) 个 band，每个 band 有 \( r \) 行。
  2. **对每个 band 进行哈希**：
     - 对 band 中的签名片段进行哈希，映射到哈希桶。
     - 如果两个文档在某个 band 中的哈希值相同，则认为它们是候选对。
  3. **参数调整**：
     - \( b \) 和 \( r \) 的选择决定了候选对的数量与误差：
       - 增大 \( b \)：减少假阳性，增加假阴性。
       - 增大 \( r \)：减少假阴性，增加假阳性。

#### Step 4: 验证候选对
- **目的**：进一步验证候选对的相似性，减少假阳性。
- **方法**：
  - 使用原始数据计算候选对的相似性（如 Jaccard 相似性）。
  - 只保留相似性超过阈值 \( s \) 的数据对。

#### 3. 示例流程
假设有以下文档集合：
- $( D_1 = \{a, b, c\}, D_2 = \{b, c, d\}, D_3 = \{c, d, e\} )$

#### 1. Shingling
将每个文档转化为集合形式：
- $( D_1 = \{a, b, c\}, D_2 = \{b, c, d\}, D_3 = \{c, d, e\})$

#### 2. MinHashing
设计 3 个哈希函数：
- $( h_1(x) = (2x + 1) \mod 5 )$
- $( h_2(x) = (3x + 2) \mod 5 )$
- $( h_3(x) = (5x + 3) \mod 5 )$

计算 MinHash 签名：

| 文档        | \( h_1 \) | \( h_2 \) | \( h_3 \) |
| --------- | --------- | --------- | --------- |
| \( D_1 \) | 1         | 2         | 3         |
| \( D_2 \) | 2         | 2         | 3         |
| \( D_3 \) | 3         | 3         | 4         |

#### 3. LSH
- 划分为 \( b = 3 \) 个 band，每个 band 有 \( r = 1 \) 行。
- 对每个 band 进行哈希：
  - 第一 band：\( h_1 \) 相同的文档为候选对。
  - 第二 band：\( h_2 \) 相同的文档为候选对。
  - 第三 band：\( h_3 \) 相同的文档为候选对。

#### 4. 验证候选对
计算候选对的 Jaccard 相似性，过滤掉低相似度的数据对。
