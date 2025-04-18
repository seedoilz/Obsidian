---
aliases:
tags:
  - code/Reinforcement-Learning
title: Markov Process
date created: 2025-04-13 18:04:00
date modified: 2025-04-13 19:04:23
---



## Markov Process

[[Markov Property]] [[Markov Chain]]

[[Markov Decision Process]]

“The future is independent of the past given the present”


"A stocastic model describing a sequence of possible events in which the probability of each event depends only on the state attained in the previous event"

here stocastic meand uncertaitity.  

A **Markov process** is the continuous-time version of a **Markov chain**.

A state $S_{t}$ is Markov if and only if:

$$\Large P [S_{t+1} | S_{t} ] = P [S_{t+1} | S_{1}, ..., S_{t} ]$$

- The state captures all relevant information from the history 
- Once the state is known, the history may be thrown away 
- i.e. The state is a sufficient statistic of the future

A Markov process is a sequence of states with the [[Markov Property]]

you can turn alost anything into a Markovian Process by making sure the the current state in the model contains everything you need to remember from the past.  

Each row of the matric in the State Transition Matrix completly characterises the transitions from one possible starting place in this Markov Process.  This single matrix gives the complete structure to this Markov Problem.

A markov process can undergo transitions from one state to aother, whee transitions between states is determined by a probability distribution.

![parkov-process.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/parkov-process.png)


For a Markov state $s$ and successor state $s'$ , the state transition probability is defined by


$$\Large P_{ss'} = P [S_{t+1} = s' | S_{t} = s]$$
State transition matrix P defines transition probabilities from all states s to all successor states s'


$$
P = from \begin{equation}
  \begin{bmatrix}
  P_{11} .. P_{1n} \\
  . \\
  . \\
  P_{n1} .. P_{nn} 
  \end{bmatrix}
\end{equation} to
$$

where each row of the matrix sums to 1.

A Markov process is a memoryless random process, i.e. a sequence of random states S1, S2, ... with the Markov property

A Markov Process (or Markov Chain) is a tuple $S,P$
- $S$ is a (finite) set of states 
- $P$ is a state transition probability matrix, 
$P_{ss'} = P [S_{t+1} = s' | S_{t} = s]$