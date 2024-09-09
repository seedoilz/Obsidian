---
aliases:
  - 比特币
title: Bitcoin
date created: 2024-09-09 19:09:00
date modified: 2024-09-09 19:09:68
---

## Bitcoin Users

### Permissionless
>Everyone can join just generates a key pair

how much bitcoin each pk/address “has” is from the blockchain state.

"**U**nspent **t**ransaction **o**utput” (UTXO)

![CleanShot 2024-09-09 at 19.31.55.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/CleanShot%202024-09-09%20at%2019.31.55.png)

> [!NOTE] PK SK Meaning
> PK is more like a bill or account. The value of the PK is defined by the blockchain state. And SK is like a password.
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
