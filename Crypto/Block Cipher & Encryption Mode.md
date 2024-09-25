---
aliases: 
title: Block Cipher & Encryption Mode
date created: 2024-09-17 19:09:00
date modified: 2024-09-18 21:09:90
tags:
  - input
---

## Linear Cipher
If additive structures are defined on both the domain _D_ a range _R_, we say that a function _f_ from _D_ to _R_ is **linear** if $f (a+b) = f (a) + f (b)$ for all a and b in D.

### Weakness
- **线性函数的弱点**：对于线性加密算法，加密和解密的运算是可预测的，且明文和密文之间的关系是线性的。这意味着只需要分析少量的明文和对应的密文对，攻击者就可以通过线性代数方法推断出密钥。
- **仿射函数的弱点**：仿射加密虽然比单纯的线性加密多了一步常数偏移，但其本质上仍然相对简单。只要攻击者掌握了足够的明文和密文对，仍然可以通过数学方法（如线性方程组求解）推断出密钥。

## Block Cipher
Block Cipher E 是一个加密函数，可以表示为：
$E: K \times M \rightarrow C$
其中：
	•	K 是密钥空间，表示所有可能的密钥集合，密钥的长度为 ( l ) 位（( K = {0,1}^l )）。
	•	M 是明文空间，表示所有可能的明文集合，明文长度为 ( n ) 位（( M = {0,1}^n )）。
	•	C 是密文空间，表示所有可能的密文集合，密文长度为 ( n ) 位（( C = {0,1}^n )）。

### 基本设计原则
#### 混淆（Confusion）
混淆的目的是隐藏密钥与密文之间的关系。也就是说，通过混淆操作，攻击者无法轻易从密文推断出密钥。加密算法设计应保证即使密钥发生了微小的变化（例如一个位的改变），密文的变化也应该非常显著。

**效果**：如果密钥的某一位发生改变，理想情况下，密文的许多位都会发生变化，这使得从密文反推出密钥变得极为困难。

#### 扩散（Diffusion）
扩散的目的是隐藏明文与密文之间的关系。通过扩散操作，明文中的每一位都影响密文中的多个位，使得明文和密文之间的统计关系变得不明显。

**效果**：如果明文的某一位发生改变，密文中许多位都会变化。这使得即使攻击者掌握了一些明文和密文对，也难以通过这些信息找出加密规律。

#### 雪崩效应（Avalanche Effect）
雪崩效应是加密算法的一个重要特性，意味着如果明文或密钥中的一位发生改变，密文中的许多位都会发生变化。这是一种极端的扩散表现。

这个准则**强调**了一个好的分组密码应该具备的**特性**：即使输入发生了最小的变化，输出也会出现显著的变化。

## Encryption Mode

### CPA
CPA安全模型（Chosen Plaintext Attack Security Model，选择明文攻击安全模型）是密码学中衡量加密方案安全性的一种标准，主要用于评估加密算法在面对特定类型攻击时的抗性。CPA安全模型假设攻击者能够选择明文并获得对应的密文，然后通过这些信息推测或破译其他密文中的信息。

#### 核心概念
1. **选择明文攻击（CPA）：** 在CPA模型下，攻击者可以选择任意明文，并要求加密系统为其加密，得到对应的密文。攻击者通过反复提出加密请求，试图推断出加密算法的内部结构或者密钥，从而破解系统。
2. **CPA安全性：** 如果一个加密算法在CPA模型下仍然无法被攻击者破解，称其为CPA安全。CPA安全要求即使攻击者可以自由选择明文，并获取其对应的密文，他也无法从这些信息中获取足够的信息来破解其他密文。
3. 
#### CPA安全模型下的对抗游戏

常用的评估CPA安全性的方式是通过“对抗性游戏”（Adversarial Game）的方式来描述。在这个游戏中：

- 攻击者首先选择两个明文 M0 和 M1​。
- 加密系统随机选择其中一个明文进行加密，并返回对应的密文给攻击者。
- 攻击者的目标是通过分析返回的密文，猜测出哪个明文被加密了。
- 攻击者可尝试多次。

如果攻击者的猜测能力不超过随机猜测的水平（50%的成功率），则说明该加密算法在CPA模型下是安全的。

**密码不是加密方案**：
- 密码是构建加密方案的基础组件。它们是更底层的加密操作，而加密方案则是对任意消息的更高层次的操作。

### how to connect these blocks during encryption?
Several Ways

#### ECB(Electronic Code Book Mode)
ECB（Electronic Code Book，电子密码本模式）是一种最基础的对称加密工作模式。在这种模式下，**消息被分割成若干独立的块，每个块独立加密**。它的工作方式简单易懂，但在实际应用中有一定的局限性。

##### ECB模式的工作原理
1. **分块处理：** 输入的明文消息会被分成多个固定长度的块（通常是128位、64位等）。
2. **独立加密：** 每个分块独立进行加密，不依赖于其他分块。每个分块使用相同的加密密钥 kkk 进行加密，得到对应的密文块。
    - 加密过程：$c_i = E(k, m_i)$，其中 $c_i$​ 表示第 $i$ 块的密文，$m_i$ 表示第 $i$ 块的明文，$E(k, m_i)$ 是使用密钥 $k$ 对明文块 $m_i$​ 进行加密。
3. **解密过程：** 解密时也遵循相同的分块方式，将每个独立的密文块解密为明文块。

***ECB mode is not CPA secure!***

#### CBC(Cipher Block Chaining)
CBC（Cipher Block Chaining，密码分组链接模式）是一种常见的对称加密模式，旨在克服ECB模式中的安全性问题。它通过将加密过程中的各个数据块关联起来，使得相同的明文块不会生成相同的密文块，从而增强了加密的安全性。

##### CBC模式的工作原理
###### 加密过程
1. 对于第一个明文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mn>1</mn></msub></mrow><annotation encoding="application/x-tex">m_1</annotation></semantics></math>，首先与初始向量 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">IV</annotation></semantics></math> 进行异或操作：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mn>1</mn></msub><mo>⊕</mo><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">m_1 \oplus IV</annotation></semantics></math>。
2. 然后将结果进行加密，生成第一个密文块：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mn>1</mn></msub><mo>=</mo><mi>E</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>m</mi><mn>1</mn></msub><mo>⊕</mo><mi>I</mi><mi>V</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">c_1 = E(k, m_1 \oplus IV)</annotation></semantics></math>。
3. 对于接下来的每个明文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">m_i</annotation></semantics></math>mi​，它与前一个密文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mrow><mi>i</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">c_{i-1}</annotation></semantics></math>​ 进行异或操作：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mi>i</mi></msub><mo>⊕</mo><msub><mi>c</mi><mrow><mi>i</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">m_i \oplus c_{i-1}</annotation></semantics></math>​。
4. 然后将结果加密，生成密文块：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mi>i</mi></msub><mo>=</mo><mi>E</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>m</mi><mi>i</mi></msub><mo>⊕</mo><msub><mi>c</mi><mrow><mi>i</mi><mo>−</mo><mn>1</mn></mrow></msub><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">c_i = E(k, m_i \oplus c_{i-1})</annotation></semantics></math>。

###### 解密过程
1. 对于第一个密文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mn>1</mn></msub></mrow><annotation encoding="application/x-tex">c_1</annotation></semantics></math>​，先用解密密钥解密得到：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>D</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>c</mi><mn>1</mn></msub><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">D(k, c_1)</annotation></semantics></math>，然后将结果与初始向量 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">IV</annotation></semantics></math>IV 进行异或运算，得到明文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mn>1</mn></msub></mrow><annotation encoding="application/x-tex">m_1</annotation></semantics></math>​：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mn>1</mn></msub><mo>=</mo><mi>D</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>c</mi><mn>1</mn></msub><mo stretchy="false">)</mo><mo>⊕</mo><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">m_1 = D(k, c_1) \oplus IV</annotation></semantics></math>。
2. 对于之后的密文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">c_i</annotation></semantics></math>ci​，首先解密得到：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>D</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>c</mi><mi>i</mi></msub><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">D(k, c_i)</annotation></semantics></math>，再与前一个密文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mrow><mi>i</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">c_{i-1}</annotation></semantics></math>​ 进行异或运算，得到明文块：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mi>i</mi></msub><mo>=</mo><mi>D</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>c</mi><mi>i</mi></msub><mo stretchy="false">)</mo><mo>⊕</mo><msub><mi>c</mi><mrow><mi>i</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">m_i = D(k, c_i) \oplus c_{i-1}</annotation></semantics></math>。

###### 优点
- **高安全性**：相较于ECB模式，CBC模式解决了相同明文块加密后生成相同密文的问题，显著提高了安全性。
- **隐藏模式信息**：明文中相同的部分不会在密文中表现出相同的模式，有效防止模式分析攻击。

###### 缺点
- **不能并行加密**：由于每个明文块的加密依赖于前一个密文块，整个加密过程是顺序的，无法并行进行，可能影响加密效率。
- **IV传输问题**：加密和解密双方都需要知道初始向量 IV，因此需要将IV与密文一起传输。虽然IV不需要保密，但需要保证其随机性和不可预测性，否则可能会削弱加密的安全性。

#### OFB(Output Feedback)
##### OFB模式的工作原理：

1. **初始向量（IV）：** 和CBC模式一样，OFB模式使用初始向量 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">IV</annotation></semantics></math>，这保证了即使相同的明文和密钥，在不同的加密操作中生成的密文也不同。
2. **反馈机制：**
    - 初始向量 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>I</mi><mi>V</mi></mrow><annotation encoding="application/x-tex">IV</annotation></semantics></math> 首先通过加密算法加密，生成一个伪随机数序列的第一个块。
    - 接下来的每个块都通过不断对前一个加密输出块进行加密生成，形成连续的伪随机数流。
3. **与明文异或：** 伪随机数流中的每一个块会与相应的明文块进行异或运算，生成密文。

###### OFB加密过程：
- 第一个加密块：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>O</mi><mn>1</mn></msub><mo>=</mo><mi>E</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><mi>I</mi><mi>V</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">O_1 = E(k, IV)</annotation></semantics></math>
- 第二个加密块：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>O</mi><mn>2</mn></msub><mo>=</mo><mi>E</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><msub><mi>O</mi><mn>1</mn></msub><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">O_2 = E(k, O_1)</annotation></semantics></math>
- 以此类推生成伪随机数流，之后每个明文块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>m</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">m_i</annotation></semantics></math>​ 与对应的加密块 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>O</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">O_i</annotation></semantics></math>​ 进行异或生成密文：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mi>i</mi></msub><mo>=</mo><msub><mi>m</mi><mi>i</mi></msub><mo>⊕</mo><msub><mi>O</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">c_i = m_i \oplus O_i</annotation></semantics></math>

##### 特点：
- **无需解密算法对称**：加密和解密的过程是相同的，因为它只是生成伪随机数流与明文进行异或。解密时，直接用伪随机数流与密文异或即可恢复明文。
- **错误传播控制：** 如果密文某个块在传输中损坏，它只会影响该块，后续块不会受到影响。
- **不可并行化**：因为每一个伪随机数块都依赖于上一个块，OFB模式的加密不能并行处理。

###### 优点：
- **适合流式加密**：OFB模式可以加密任意长度的明文，非常适合需要逐字节或逐比特加密的场景。
- **错误传播有限**：在通信中的错误只影响一个加密块，而不会扩散到后续块。

###### 缺点：
- **伪随机数流的安全性依赖于IV**：初始向量 IVIVIV 必须是唯一且不可预测的，否则会削弱加密的安全性。

#### CTR（Counter，计数器）
##### CTR模式的工作原理：
1. **计数器（Counter）：** CTR模式使用一个计数器，该计数器与初始值一起形成一个唯一的输入序列。计数器从一个初始值开始递增，并作为每个块的输入，与密钥一起经过加密算法生成伪随机数。
2. **与明文异或：** 与OFB一样，伪随机数流中的每个块与相应的明文块进行异或运算，生成密文。

##### CTR加密过程：
- 计数器输入块：每个块的输入值由初始计数器值加上块编号构成，如 <math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>I</mi><mi>V</mi><mo separator="true">,</mo><mi>I</mi><mi>V</mi><mo>+</mo><mn>1</mn><mo separator="true">,</mo><mi>I</mi><mi>V</mi><mo>+</mo><mn>2</mn><mo separator="true">,</mo><mo>…</mo></mrow><annotation encoding="application/x-tex">IV, IV+1, IV+2, \dots</annotation></semantics></math>
- 对于每个计数器块，使用密钥进行加密：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>O</mi><mi>i</mi></msub><mo>=</mo><mi>E</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><mi>I</mi><mi>V</mi><mo>+</mo><mi>i</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">O_i = E(k, IV + i)</annotation></semantics></math>
- 每个明文块与相应的伪随机数块异或生成密文：<math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>c</mi><mi>i</mi></msub><mo>=</mo><msub><mi>m</mi><mi>i</mi></msub><mo>⊕</mo><msub><mi>O</mi><mi>i</mi></msub></mrow><annotation encoding="application/x-tex">c_i = m_i \oplus O_i</annotation></semantics></math>

##### 特点：
- **并行处理：** 由于每个块的加密只依赖于当前计数器值，不需要依赖前一个加密块，因此CTR模式可以并行处理，提高加密效率。
- **灵活性：** 由于伪随机数流的生成只依赖于计数器值，可以在任意位置开始加密或解密数据块，而不必依赖前面的块。
- **无需解密算法对称**：加密和解密过程都是通过生成伪随机数流，与密文或明文进行异或完成，因此算法是对称的。

###### 优点：
- **高效并行化：** 由于每个块都可以独立生成伪随机数流，CTR模式非常适合高性能环境，尤其是在并行计算中。
- **随机访问：** CTR允许在加密流中任意块的随机访问，因此可以对特定数据块进行加密和解密，而不需要处理整个数据流。
- **灵活性高**：适合流式数据的加密，比如大数据文件或网络数据传输。

###### 缺点：
- **计数器不能重复**：计数器值必须唯一且不可预测，否则攻击者可能通过分析重复的伪随机数块破解加密内容。

## Padding

### 填充（Padding）的必要性
- **分组加密**（Block cipher）会将明文分割成多个块进行加密，而每个分组的大小必须等于加密算法的块大小。例如AES算法的块大小为128位（16字节）。
- **问题**：明文的最后一个块不一定刚好等于块大小（比如明文长度不是16字节的倍数），这时候需要对明文进行填充，使最后一个块达到规定的大小。
- **解决办法**：使用填充技术来确保明文长度是块大小的整数倍。通常会添加额外的字节到最后一个分组，填充后再进行加密。

### PKCS#7 填充方式
- **PKCS#7填充**是最常用的填充方案之一。
- 它通过在最后一个分组后面添加N个字节，且每个字节的值等于`chr(N)`，N为需要填充的字节数。例如，如果需要填充4个字节，那么这4个字节的值全部是`0x04`。