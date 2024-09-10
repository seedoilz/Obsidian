---
aliases: 
title: Stream Cipher & Random Number Generation
date created: 2024-09-10 19:09:00
date modified: 2024-09-10 19:09:47
tags:
  - input
---

## Cipher ways
### Variable-Length One-Time Pad
在One-Time Pad的基础上确保每一个message都小于key。

### Substitution Cipher
># 替代密码

所谓替代就是将明文中的一个字母由其他字母、数字或符号替换的一种方法。替代密码 (substitution cipher) 是指先建立一个替换表，加密时将需要加密的明文依次通过查表，替换为相应的字符，明文字符被逐个替换后，生成无任何意义的字符串，即密文；解密时则利用对应的逆替换表，将需要解密的密文依次通过查表，替换为相应的字符即可恢复出明文。替代密码的密钥就是其替换表。
根据密码算法加解密时使用替换表多少的不同，替代密码又可分为单表替代密码和多表替代密码。

## Perfect Security
### Definition
A symmetric encryption system is perfectly secure if for **any two messages** $M_0$,$M_1$ in the plaintext space and any C
	$Pr[E(K,M_0)]=C = Pr[E(K,M_1)=C]$

### Implications（推论）
- For the same ciphertext, the probability that it comes from $M_0, M_1$ is the same. That is, any plaintext could become a given ciphertext.
- Given C, and even knowing that the message is either M0 or M1, the attacker cannot tell which one.

#### How to prove one encryption system is perfect (or not)?
![CleanShot 2024-09-10 at 19.44.42.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-09-10%20at%2019.44.42.png)



![CleanShot 2024-09-10 at 19.52.42.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-09-10%20at%2019.52.42.png)

- It shows that perfect security is such a powerful notion that no one can really do no better than the one-time pad:
	- keys must be at least as long as messages. As a result, it is almost impossibleto use perfectly secure ciphers in practice

## StreamCipher
According to Shannon’s theorem, the only way to achieve perfect security is to have keys that are as long as messages.

### Requirements
- Consider not all possible adversaries, but only computationally feasible adversaries.
- That is, “real world” adversaries that must perform their calculations on real computers using a reasonable amount of time and memory. This will lead to a**weaker definition** of security called **semantic security**.

### Pseudorandom Generator（PRG）
It takes a short, **truly random secret seed/key k** and expands it into a longer **“random-looking” sequence**. PRG is a deterministic procedure.

本质上讲，PRG就是一个高效的、确定的、不可预测的函数 G ，能将一个长度为 s 的二进制数字符串，扩充为一个长得多的、长度为 n 的二进制数字符串。即：

#### Security of PRG
我们说一个PRG是安全的，就意味着我们认为这个PRG的输出是**均匀**的。也就是说，**一个PRG** G **是安全的，当且仅当它的输出分布与均匀随机分布_不可区分_**。即：
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240910230341.png)


