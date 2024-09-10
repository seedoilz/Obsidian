---
aliases:
  - 比特币
title: Bitcoin
date created: 2024-09-09 19:09:00
date modified: 2024-09-09 20:09:59
---

## Bitcoin Users

### Permissionless
>Everyone can join just generates a key pair

how much bitcoin each pk/address “has” is from the blockchain state.

#### "**U**nspent **t**ransaction **o**utput” (UTXO)
A user can have multiple such key pairs, each is “unspent”

![CleanShot 2024-09-09 at 19.31.55.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-09-09%20at%2019.31.55.png)

> [!NOTE] PK SK Meaning
> PK is more like a bill or account. The value of the PK is defined by the blockchain state. And SK is like a password. 假设PK所对应的金额没有花费完，它就会转换成一个新的PK对应新的面额。
> So, it has these three properties.
> - It’s created by a transaction.
> - It’s not your “account.”
> - It’s only spent once (need to spend it all).

### How to keep safety?
The BLOCKCHAIN emulate a public write-only (precisely, append-only) bulletin-board containing a list of transactions.

The blockchain is maintained by miners. However, miners could be cheaters. So, a way should be raised to help maintain the safety. "The majority wins" could help this circumstance since if cheaters didn't exceed the honest miners, the blockchain is safe.

#### How to define majority?
**Majority** is defined as the majority of **computational power**!
**Sybil** creation **doesn’t increase** attackers’ computational power.

> [!NOTE] Power
> “Measures” a user’s computational power
> by how much time is needed for solving a “puzzle”
> the puzzle should be difficult to solve
> but a solution should be _easily verifiable_
> In Bitcoin, it is based on the cryptographic hash functions


### Main Principles
1. It is **computationally hard** to extend the chain.
2. Once a miner finds an extension, (s)he **broadcasts it to everybody**.
3. The users will always accept “**the longest chain**” as the valid one.

### How a bitcoin transaction is made?
假设用户 A 想向用户 B 发送 1 个比特币 （BTC）。
当用户 A 发起交易时，有关发送者和接收者的信息被打包并在一个区块上加盖时间戳，并发送到称为内存池（内存池的缩写）的队列，在那里它将等待验证并添加到区块链中。
成功发现区块的矿工将进行批量交易，并验证包括数字签名、消息和公钥在内的所有信息是否合法。
一旦信息得到验证，该区块就会被广播到网络中的所有节点，这些节点必须在将该区块添加到官方链之前检查并同意该区块有效。确认比特币交易的平均时间约为 10 分钟。
该过程完成后，用户 B 将收到用户 A 发送给他们的 1 个 BTC，网络上的所有节点都将同意使用所选共识模型进行交易，比特币矿工将获得验证交易成功的奖励。关于该交易的新信息块现在作为无限公有链的一部分相互链接。

#### 如何处理Blockchain的fork？
Always Longest Chain wins.
所以，transaction可能会失败，需要等待6个block（也就是1小时）。

### 加入Blockchain的动机
所有Miners都能从**自己找到并且成功加入Blockchain**（其实也就意味着，需要很快找到，并且验证完毕后broadcast to all the other miners to verify）的transaction中获得奖励。