---
aliases:
tags:
title: Huffman Coding
date created: 2025-02-06 20:02:00
date modified: 2025-02-06 20:02:80
---
## Fixed-length(FLC) v.s Variable-length(VLC)
### Fixed-length coding
all symbols have the same coding length
e.g. 64 symbols -> 6 bits minimum

### Variable-length coding
- Rare symbols -> more bits
- Frequent symbols -> less bits
- E.g. use 1 bit for most frequent color, and many bits for less used color

#### How to achieve VLC?
One code cannot be a prefix another code so that we could decode.


## Huffman Coding
### Definition
A VLC (Variable-length coding).
Which can achieve the lower boud of the average bit rate

### Procedure
1. **统计字符频率**： 首先，计算输入数据中每个字符的出现频率。
2. **构建霍夫曼树**：
    - 创建一个最小堆（优先队列），其中每个节点存储字符及其对应的频率。
    - 从最小堆中取出频率最低的两个节点，创建一个新节点，其频率为这两个节点的频率之和。新节点的左右子树分别是这两个频率最低的节点。
    - 将新节点插入堆中，重复这个过程直到堆中只剩下一个节点，这个节点就是霍夫曼树的根节点。
3. **生成霍夫曼编码**：
    - 从根节点开始，沿着树的每条路径向下走。每经过一个左子树，给编码添加一个'0'，每经过一个右子树，给编码添加一个'1'。
    - 最终每个字符都会对应一个唯一的二进制编码。

```python
import heapq

# 生成霍夫曼树并返回霍夫曼编码
def huffman_encoding(data):
    # 统计字符频率
    frequency = {}
    for char in data:
        if char in frequency:
            frequency[char] += 1
        else:
            frequency[char] = 1

    # 创建最小堆
    heap = [[weight, [char, ""]] for char, weight in frequency.items()]
    heapq.heapify(heap)

    # 构建霍夫曼树
    while len(heap) > 1:
        lo = heapq.heappop(heap)
        hi = heapq.heappop(heap)
        for pair in lo[1:]:
            pair[1] = '0' + pair[1]
        for pair in hi[1:]:
            pair[1] = '1' + pair[1]
        heapq.heappush(heap, [lo[0] + hi[0]] + lo[1:] + hi[1:])

    # 返回霍夫曼编码
    huffman_tree = heap[0]
    return sorted(huffman_tree[1:], key=lambda p: (len(p[-1]), p))

# 示例数据
data = "this is an example for huffman encoding"
huffman_code = huffman_encoding(data)

# 输出霍夫曼编码
for char, code in huffman_code:
    print(f"{char}: {code}")
```
