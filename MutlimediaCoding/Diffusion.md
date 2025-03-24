---
aliases: 
tags:
  - code/coding
title: Diffusion
date created: 2025-03-23 13:03:00
date modified: 2025-03-23 15:03:15
---
## DDPM算法
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20250323133331.png)
### 前向加噪 forward
从$x_0$ 到$x_T$ 的过程就是**前向加噪**过程，我们可以看到加噪过程顾名思义就是对原始图片$x_0$ 进行了一系列操作，使其变得 "模糊" 起来，而与之相对应的去噪过程就特别像还原过程，使得图片变得清晰。

这里的操作实际上就是指在图片加入噪声$noise$，噪声$noise$ 本身的分布可以是很多样的，而论文中采用的是**标准正态分布**，其理由是考虑到**其优良的性质**——**叠加性**。

我们通过往图片中加入噪声，使得图片变得模糊起来，当加的步骤足够多的时候（也就是 T 的取值越大的时候），图片已经非常接近一张纯噪声。纯噪声也就意味着多样性，我们的模型在去噪（还原）的过程中能够产生更加多样的图片。

$x_0$ 是原始图片，其满足初始分布$q(x_0)$，即$x_0 \sim q(x_0)$

对于$t \in [1,T]$ 时刻，$x_t$ 和$x_{t-1}$ 满足如下关系式

$$
\displaylines{x_t = \sqrt{1-\beta_t}x_{t-1} + \sqrt{\beta_t}\epsilon \\ \epsilon \sim N(0,1)\epsilon \sim N(0,1)}
$$

令$\alpha_t = 1 - \beta_t$，则公式变形为

$$
x_t = \sqrt{\alpha_t}x_{t-1} + \sqrt{1-\alpha_t}\epsilon \\
$$
其中的$\beta_t$ 是固定常数，其随着 t 的增加而增加

继续进行推导
$$\displaylines{x_t = \sqrt{\alpha_t}x_{t-1} + \sqrt{1-\alpha_t}\epsilon \\= \sqrt{\alpha_t}(\sqrt{\alpha_{t-1}}x_{t-2}+\sqrt{1-\alpha_{t-1}}\epsilon) + \sqrt{1-\alpha_t}\epsilon \\= \sqrt{\alpha_t\alpha_{t-1}}x_{t-2}+\sqrt{\alpha_t}\sqrt{1-\alpha_{t-1}}\epsilon + \sqrt{1-\alpha_t}\epsilon}$$

在这里引出 $\epsilon$ 是噪声，具有 $\epsilon \textasciitilde N(0,1)$ 。

$$\displaylines{X_1 \sim \sqrt{\alpha_t}\sqrt{1-\alpha_{t-1}}\epsilon = N(0,{\alpha_t(1-\alpha_{t-1})}) \\
X_2 \sim \sqrt{1-\alpha_t}\epsilon = N(0,{1-\alpha_{t}}) \\
X_1 + X_2 = N(0,1-{\alpha_t\alpha_{t-1}}) \\}
$$
则公式可以进一步简化为
$$
x_t = \sqrt{\alpha_t\alpha_{t-1}}x_{t-2} + (\sqrt{1-\alpha_t\alpha_{t-1}})\epsilon \\
$$

> [!NOTE] 正态分布的性质
> 设 $x \sim N(\mu, \sigma^2)$，若我们做线性变换 $y = a x + b$，则：
> 
> $$  
> y \sim N(a\mu + b, a^2\sigma^2)  
> $$
> 
> 对于标准正态的情况：
> 如果 $x \sim N(0,1)$，那么：$$kx \sim N(0, k^2)$$

由数学归纳法，可以进一步推导得
$$x_t = \sqrt{\alpha_t\alpha_{t-1}...\alpha_1}x_{0} + (\sqrt{1-\alpha_t\alpha_{t-1}\alpha_{1}})\epsilon \\
$$
令$\bar{\alpha_t} = {\alpha_t\alpha_{t-1}...\alpha_1}$，则公式可以进一步化简为
$$
x_t = \sqrt{\bar{\alpha_t}}x_0 + \sqrt{1-\bar{\alpha_t}}\epsilon \\
$$
### 反向去噪 reverse
去噪的过程中，我们需要**根据当前时刻的图片去预测前一时刻的图片**，也就意味着去除一部分噪声，还原到上一时刻的图片。

在去噪过程中，我们并不知道上一时刻$x_{t-1}$ 的值，是需要用$x_t$ 进行预测，所以我们只能用概率的形式，采用贝叶斯公式去计算后验概率$P(x_{t-1}|x_t)$

$$P(x_{t-1}|x_t) = \frac{P(x_{t-1}x_t)}{P(x_t)} = \frac{P(x_t|x_{t-1})P(x_{t-1})}{P(x_t)} \\
$$

进一步在已知原图$x_0$ 的情况下，进行公式改写

$$P(x_{t-1}|x_t,x_0) = \frac{P(x_t|x_{t-1},x_0)P(x_{t-1}|x_0)}{P(x_t|x_0)} \\
$$


> [!NOTE] 概率替换原因
> 在训练阶段，我们是有原图 $x_0$ 的。所以我们可以利用它，把 $P(x_{t-1} \mid x_t, x_0)$ 推导成一个高斯分布，并用它来构造训练目标，让模型去逼近 $P(x_{t-1} \mid x_t)$。
> 
> 例如在 DDPM 中，模型训练的目标是：
> $$
> \text{让 } q(x_{t-1} \mid x_t, x_0) \approx p_\theta(x_{t-1} \mid x_t)
> $$


等式右边部分都变成先验概率，我们由前向加噪过程即可对公式进行改写，依据为$x_t = \sqrt{\bar{\alpha_t}}x_0 + \sqrt{1-\bar{\alpha_t}}\epsilon$ 和$x_t = \sqrt{1-\beta_t}x_{t-1} + \sqrt{\beta_t}\epsilon$ 两个公式

$$P(x_{t-1}|x_t,x_0) = \frac{N(\sqrt{\alpha_t}x_{t-1},1-\alpha_t) N(\sqrt{\bar{\alpha_{t-1}}}x_0,1-\bar{\alpha_{t-1}})}{N(\sqrt{\bar{\alpha_{t}}}x_0,1-\bar{\alpha_{t}})}\\
$$

在前面的小节中，我们已经给出了正态分布的概率密度函数，我们可以对上述公式进行展开。展开的依据为

展开的时候注意$x_t$ 和$x_{t-1}$ 的区别，在 | 前的变量才是概率密度函数 f(x) 里的 x，$\propto$ 代表成正比，即我们不关心前面的系数

$P(x_{t-1}|x_t,x_0) \propto exp -\frac{1}{2}[\frac{(x_t-\sqrt{\alpha_t}x_{t-1})^2}{1-\alpha_t} + \frac{(x_{t-1}-\sqrt{\bar{\alpha_{t-1}}}x_0)^2}{1-\bar{\alpha_{t-1}}} - \frac{(x_t-\sqrt{\bar{\alpha_t}}x_0)^2}{1-\bar{\alpha_t}}]$

此时由于$x_{t-1}$ 是我们关注的变量，所以整理成关于$x_{t-1}$ 的形式

$P(x_{t-1}|x_t,x_0) \propto exp -\frac{1}{2}[(\frac{\alpha_t}{1-\alpha_t}+\frac{1}{1-\bar{\alpha_{t-1}}})x_{t-1}^2 - (\frac{2\sqrt{\alpha_t}}{1-\alpha_t}x_t+\frac{2\sqrt{\bar{\alpha_{t-1}}}}{1-\bar{\alpha_{t-1}}}x_0)x_{t-1} + C(x_t,x_0)]$，其中$C(x_t,x_0)$ 与$x_{t-1}$ 无关，只影响最前面的系数

由于标准正态分布满足 $\propto exp -\frac{x^2+\mu^2-2x\mu}{2\sigma^2}$，则

$$\frac{1}{\sigma^2} = \frac{\alpha_t}{1-\alpha_t} + \frac{1}{1-\bar{\alpha_{t-1}}} = \frac{1-\bar{\alpha_t}}{(1-\alpha_t)(1-\bar{\alpha_{t-1}})} \\
$$

$$\sigma^2 = \frac{\beta_t(1-\bar{\alpha_{t-1}})}{1-\bar{\alpha_t}} \\
$$

$$\mu = \frac{1}{2}\sigma^2(\frac{2\sqrt{\alpha_t}}{1-\alpha_t}x_t+\frac{2\sqrt{\bar{\alpha_{t-1}}}}{1-\bar{\alpha_{t-1}}}x_0) = \frac{\sqrt{\bar{\alpha_{t-1}}}(1-\alpha_t)}{1-\bar{\alpha_t}}x_0 + \frac{(1-\bar{\alpha_{t-1}})\sqrt{\alpha_t}}{1-\bar{\alpha_{t}}}x_t \\
$$
在前面的小节中，我们已经给出了正态分布的概率密度函数，我们可以对上述公式进行展开。展开的依据为

展开的时候注意$x_t$ 和$x_{t-1}$ 的区别，在 | 前的变量才是概率密度函数 f(x) 里的 x，$\propto$ 代表成正比，即我们不关心前面的系数

$P(x_{t-1}|x_t,x_0) \propto exp -\frac{1}{2}[\frac{(x_t-\sqrt{\alpha_t}x_{t-1})^2}{1-\alpha_t} + \frac{(x_{t-1}-\sqrt{\bar{\alpha_{t-1}}}x_0)^2}{1-\bar{\alpha_{t-1}}} - \frac{(x_t-\sqrt{\bar{\alpha_t}}x_0)^2}{1-\bar{\alpha_t}}]$

此时由于$x_{t-1}$ 是我们关注的变量，所以整理成关于$x_{t-1}$ 的形式

$P(x_{t-1}|x_t,x_0) \propto exp -\frac{1}{2}[(\frac{\alpha_t}{1-\alpha_t}+\frac{1}{1-\bar{\alpha_{t-1}}})x_{t-1}^2 - (\frac{2\sqrt{\alpha_t}}{1-\alpha_t}x_t+\frac{2\sqrt{\bar{\alpha_{t-1}}}}{1-\bar{\alpha_{t-1}}}x_0)x_{t-1} + C(x_t,x_0)]$，其中$C(x_t,x_0)$ 与$x_{t-1}$ 无关，只影响最前面的系数

由于标准正态分布满足 $\propto exp -\frac{x^2+\mu^2-2x\mu}{2\sigma^2}$，则

$$\frac{1}{\sigma^2} = \frac{\alpha_t}{1-\alpha_t} + \frac{1}{1-\bar{\alpha_{t-1}}} = \frac{1-\bar{\alpha_t}}{(1-\alpha_t)(1-\bar{\alpha_{t-1}})} \\
$$

$$\sigma^2 = \frac{\beta_t(1-\bar{\alpha_{t-1}})}{1-\bar{\alpha_t}} \\
$$

$$\mu = \frac{1}{2}\sigma^2(\frac{2\sqrt{\alpha_t}}{1-\alpha_t}x_t+\frac{2\sqrt{\bar{\alpha_{t-1}}}}{1-\bar{\alpha_{t-1}}}x_0) = \frac{\sqrt{\bar{\alpha_{t-1}}}(1-\alpha_t)}{1-\bar{\alpha_t}}x_0 + \frac{(1-\bar{\alpha_{t-1}})\sqrt{\alpha_t}}{1-\bar{\alpha_{t}}}x_t \\
$$
总结一下，$P(x_{t-1}|x_t) = N(\frac{1}{\sqrt{\alpha_t}}(x_t - \frac{1-\alpha_t}{\sqrt{1-\bar{\alpha_t}}}\epsilon),\frac{(1-\alpha_t)(1-\bar{\alpha_{t-1}})}{1-\bar{\alpha_t}})$

## DDIM
DDPM 看起来似乎很完美，但其有一个致命的缺点便是**推理速度过慢**，无法避免的迭代过程。为什么无法避免迭代过程是因为，**其本身是一个马尔科夫链的过程**，即前后时刻数据有非常紧密的绑定关系，无法进行跳跃预测，比如说用$x_t$ 直接去预测$x_{t-2}$。

但是想要高质量的生成图片，就意味着 T 要取一个比较大的值，用一张 4090 显卡进行推理，T=1000 的情况下，推理 64 张 32\*32 图片需要耗时 20s，这个时间显然是无法忍受的。如何加速推理步骤，这个时候救星 - DDIM 出现了，DDIM 通过数学推理，**打破了马尔科夫链的过程**，最为巧妙的是其无需重新训练 DDPM（无需改变前向加噪），只对采样器进行修改即可，修改后的采样器能够大幅增加采样速度。

下面我们就来分析 DDIM 的原理，从一个假设出发，假设$P(x_{t-1}|x_t,x_0)$ 满足如下正态分布则

$$P(x_{t-1}|x_t,x_0) \sim N(kx_0+mx_t,\sigma^2) \\ x_{t-1} = kx_0 + mx_t + \sigma\epsilon \epsilon \sim N(0,1) \\
$$

又因为加噪过程满足公式

$$x_t = \sqrt{\bar{\alpha_t}}x_0 + \sqrt{1-\bar{\alpha_t}}\epsilon \epsilon \sim N(0,1) \\
$$

进行代入和合并同类项

$$x_{t-1} = kx_0 + m[\sqrt{\bar{\alpha_t}}x_0 + \sqrt{1-\bar{\alpha_t}}\epsilon] + \sigma\epsilon \\ x_{t-1} = (k+m\sqrt{\bar{\alpha_t}})x_0 + \epsilon' \epsilon' \sim N(0,m^2(1-\bar{\alpha_t})+\sigma^2) \\
$$

同样地，

$$x_{t-1} = \sqrt{\bar{\alpha_{t-1}}}x_0 + \sqrt{1-\bar{\alpha_{t-1}}}\epsilon \\
$$

则满足对应系数相同

$$k + m\sqrt{\bar{\alpha_t}} = \sqrt{\bar{\alpha_{t-1}}} \\ m^2(1-\bar{\alpha_t})+\sigma^2 = 1 - \bar{\alpha_{t-1}} \\
$$

求得

$$m = \frac{\sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}}{\sqrt{1-\bar{\alpha_t}}} \\ k = \sqrt{\bar{\alpha_{t-1}}} - \frac{\sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}}{\sqrt{1-\bar{\alpha_t}}}\sqrt{\bar{\alpha_t}} \\
$$

则表达式为

$P(x_{t-1}|x_t,x_0) \sim N((\sqrt{\bar{\alpha_{t-1}}} - \frac{\sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}}{\sqrt{1-\bar{\alpha_t}}}\sqrt{\bar{\alpha_t}})x_0 + (\frac{\sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}}{\sqrt{1-\bar{\alpha_t}}})x_t,\sigma^2) \\ \sim N(\sqrt{\bar{\alpha_{t-1}}}x_0 + \sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}\frac{x_t - \sqrt{\bar{\alpha_t}}x_0}{\sqrt{1-\bar{\alpha_t}}},\sigma^2)$

**采用与反向去噪同样的原理**，将上述公式的$x_0$ 进行替换，这里采用$\epsilon_t$ 是因为前文已经说明过采用的是模型$model$ 预测的正态分布

$$x_0 = \frac{x_t - \sqrt{1-\bar{\alpha_t}}\epsilon_t}{\sqrt{\bar{\alpha_t}}} \\ \epsilon_t = \frac{x_t - \sqrt{\bar{\alpha_t}}x_0}{\sqrt{1-\bar{\alpha_t}}} \\
$$

最终得到结果，后面的$\epsilon$ 采用的是随机采样`torch.randn_like(x_t)`

$$x_{t-1} = \sqrt{\bar{\alpha_{t-1}}}(\frac{x_t - \sqrt{1-\bar{\alpha_t}}\epsilon_t}{\sqrt{\bar{\alpha_t}}}) + \sqrt{1-\bar{\alpha_{t-1}}-\sigma^2}\epsilon_t + \sigma^2\epsilon \\
$$

由于全程推导并未使用$\sigma^2$ 则其取值可以为 0。且全程推导没有用到马尔可夫的过程$x_t = \sqrt{\alpha_t}x_{t-1} + \sqrt{1-\alpha_t}\epsilon$ 可以认为不需要严格的由$x_t$ 算到$x_{t-1}$，令$x_{prev}$ 替代$x_{t-1}$，则公式变形为

$$x_{prev} = \sqrt{\bar{\alpha_{prev}}}(\frac{x_t - \sqrt{1-\bar{\alpha_t}}\epsilon_t}{\sqrt{\bar{\alpha_t}}}) + \sqrt{1-\bar{\alpha_{prev}}-\sigma^2}\epsilon_t + \sigma^2\epsilon \\
$$

其中的$x_t$ 和$x_{prev}$ 可以相隔多个迭代步数