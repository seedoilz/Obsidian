---
aliases: 
title: Diffie-Hellman Key Exchange
date created: 2024-09-24 21:09:00
date modified: 2024-09-25 15:09:64
---

## 1. Diffie-Hellman Key Exchange
### 简介  
Diffie-Hellman 密钥交换是一种允许两方在不直接共享私密信息的情况下生成共享密钥的协议。

### 步骤：
1. 双方选择一个素数 $p$ 和一个基数 $g$。
2. 每方生成一个私钥（$a$ 和 $b$），并计算各自的公钥：
   - Alice 计算 $A = g^a \mod p$
   - Bob 计算 $B = g^b \mod p$
3. 双方交换公钥 $A$ 和 $B$。
4. 使用对方的公钥计算共享密钥：
   - Alice 计算 $K = B^a \mod p$
   - Bob 计算 $K = A^b \mod p$
5. 最终共享密钥 $K = g^{ab} \mod p$。

**安全性**：协议的安全性基于离散对数问题的困难性。
