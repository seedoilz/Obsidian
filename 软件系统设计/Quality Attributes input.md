#input 

## 需求
### 功能性需求
定义系统必须做什么，并且强调系统如何提供价值给涉众。

### 质量需求
1. 系统应在功能性需求之上提供的整个系统的合乎需求的特性。
2. 质量属性是由软件系统的业务目标所决定的。
3. 分为两类
	1. 执行过程中可观察 (外部): 系统满足其行为要求的程度如何? 例如性能、安全性、可用性等
	2. 执行期间不可观察 (内部): 系统的维护、集成或测试有多容易? 例如可修改性、可移植性、可重用性和可测试性等。

## 质量属性
### 分类
Adaptability 适应性、Extensibility 可扩展性、Availability 可用性、Modularity 模块化、Configurability 灵活性、Portability 可移植性、Flexibility 灵活性、Reusability 可重用性、Interoperability 互操作性、Testability 可测试性、 Performance 性能、Auditability 可审核性、Reliability 可靠性、Maintainability 可维护性、Responsiveness 响应性、Manageability 可管理性、Recoverability 可恢复性、Sustainability 可持续性、Scalability 可扩展性、Supportability 可支持性、 Stability 稳定性、Usability 可用性、Security 安全性。

### 质量属性方案
质量属性方案用于定义所需的质量属性，是具有一定结构的简单句子。方案的主要类别为通用方案与具体方案。

#### 通用方案
与系统无关的方案，用于指导质量属性要求的规范。

#### 具体方案
具体方案是系统特定方案，用于指导特定系统的质量属性要求的规范，是通用方案的实例。

### 方案模版：表与 Stimulus-Response 图
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230424180209.png)
>刺激 (Stimulus): 到达系统时需要考虑的条件。
>刺激源 (Source of Stimulus):产生刺激的实体 (人，系统或任何其他触发)，可能是输入、信息等，对当前的状态的一个变化。
>响应 (Response): 刺激到来后工件开展的行为。
>响应度量 (Response Measure): 对刺激的响应以某种方法进行测量，以便可以测试需求 (比如多长时间系统有反馈)
>环境 (Environment): 发生刺激时系统的状况，例如系统正常运行、系统过载、系统受到攻击、系统网络出现故障等。
>工件 (Artifact):完成需求的整个系统或者系统的一部分 (软件制品)。

## 决策




