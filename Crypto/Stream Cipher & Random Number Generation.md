---
aliases: 
title: Stream Cipher & Random Number Generation
date created: 2024-09-10 19:09:00
date modified: 2024-09-11 20:09:44
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

##### Formal Definition
$$\text{Adv}_{\text{PRG}}[A, G] = \left| \Pr_{k \leftarrow \mathcal{K}}[A(G(k)) = 1] - \Pr_{r \leftarrow \{0, 1\}^n}[A(r) = 1] \right|$$
简而言之，就是给定一个算法A判断输出是来自伪随机生成器 G 还是完全随机序列。判别器会输出 0 或 1：
- 输出 1 表示 A 认为输入是由伪随机生成器 G 生成的。
- 输出 0 表示 A 认为输入是完全随机的。
如果 $\text{Adv}$ 接近 0，则算法 A 无法区分 G(k) 的输出和一个随机数，即 G 是安全的。


##### 两个证明例子
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240911165116.png)
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240911165126.png)

## Modulo
>取余

Modular arithmetic is a special kind of integer arithmetic that deduces all numbers to one of a fixed set
$$
[0, 1, 2, …, n-1]
$$
### Congruences
Two integers $a$ and $b$ are said to be congruent modulo $n$, if $a – b$ is divisible by $n$.

## Random Number Generation
A truly random number is totally unpredictable. 
However, computers can only generate pseudorandom numbers Only **practically** or **computationally unpredictable**

### MidSquare Method
#### Steps
- Let 𝑍𝑖 be a 4-digit positive integer
- Calculate $𝑍_𝑖$, which becomes an 8-digit number
- Take the middle 4 digits as 𝑍𝑖+1
- Let $𝑈_{𝑖+1} = \frac{𝑍_𝑖+1}{1000}$
- Repeat

#### Issue
Strong tendency to degenerate rapidly to 0 and stay there forever

### Linear Congruential Generators (LCGs)
#### Steps
- Start with a seed
- Typically an integer 𝑍0
- Iterative relation (similar to a hash):
$𝑍_𝑛 = (𝑎𝑍_{𝑛−1} + 𝑐)\ 𝑚𝑜𝑑\ 𝑚$
- Generate the random number stream using
$𝑈_𝑛 = 𝑍_𝑛/𝑚$

#### Examples
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240911193353.png)


> [!NOTE] looping
> The “looping” behavior of LCG is inevitable
>The length of a cycle is called the period of a generator
>The period is at most 𝑚
>If the LCG is full period, the choice of seed is not important
>In the examples, any seed between 0 to 15 can produce the entire cycle

#### Lehmer Random Number Generator
Lehmer 随机数生成器是 LCG 的一种特例，它的定义为：
$Zn=(aZn−1)\ mod  m$

Lehmer 随机数生成器的特殊之处在于：
- 增量 $c$ 被设为 0，即不进行任何加法运算。
- $m$ 通常选为一个素数，特别是一个大素数。
- $a$ 通常选为 $m$ 模下的原始元，以获得最大周期性。

#### 模数的选择
LCG 中的模数的选择，其中 b 表示一个字中包含的比特数（即位数）。具体内容如下：
当 $m$ 选为 $2^b$ 时，由于整数溢出特性，实际上不需要显式地进行模运算，计算机会自动处理。例如，当 b=32 时，32 位整数的运算会自动限制在 0 到 $2^{32} - 1$ 之间。

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20240911200614.png)
