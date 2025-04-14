---
aliases:
tags:
  - code/Reinforcement-Learning
title: Reward
date created: 2025-04-13 18:04:00
date modified: 2025-04-13 19:04:28
---
## Reward

[[Reinforcement Learning]]

Reward r is a scalar feedback provided by the environment after a particular action is taken in a given state. The total reward that the agent receives is delayed and not instantaneous.

The agent can prioritise over immediate or future reward. The return Rt\=∑k\=0∞γkrt+k is the discounted, accumulated reward with the discount factor γ ∈ (0, 1\]. Discount factor γ=0 means agent is very short sighted whereas γ=1 means the agent is very careful about future possible rewards.

The agent aims to maximize the expectation of such long term return from each state.

![reward.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/reward.png)
