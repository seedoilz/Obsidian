---
aliases:
tags:
  - code/Reinforcement-Learning
title: Bellman Equation
date created: 2025-04-13 18:04:00
date modified: 2025-04-13 19:04:21
---




#### Bellman Expectation Equation

The state-value function can again be decomposed into immediate  
reward plus discounted value of successor state,

$$
v_{\pi}(s) = \mathbb{E}_{\pi} \left[ R_{t+1} + \gamma v_{\pi}(S_{t+1}) \mid S_t = s \right]
$$
The action-value function can similarly be decomposed,

$$
q_{\pi}(s, a) = \mathbb{E}_{\pi} \left[ R_{t+1} + \gamma q_{\pi}(S_{t+1}, A_{t+1}) \mid S_t = s, A_t = a \right]
$$

v tells us how goodi is it to be in a particular state, q tells us how good is it to take a particular action.