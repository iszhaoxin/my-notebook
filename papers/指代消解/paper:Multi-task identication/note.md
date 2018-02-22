## Multi-Task Identification of Entities, Relations, and Coreference



### 1. Introduction

#### 1.1 Motivaiton

本文聚焦于科学论文的基本文本分析, 包括实体抽出, 关系抽出以及指代消解三个方向.

主要是应对大量的论文的产出, 需要一个高效率的手段将论文的知识结构化.

本文最开始的出发点是论文的实体抽出和关系抽出, 但是由于论文中经常用到指代, 这样就给信息抽出的任务带来的很大的难, 因此该研究将指代消解也融合进到了研究之中.



#### 1.2 Novelty

- 相比于其他的将三个任务分开来做的方法, 本研究采用了multi-task的方式, 将三个任务结合起来, 使三个任务的底层向量是共享的. 	
- 并且这个任务可以列举所有可能的span. 这里的span的含义我认为是不同备选实体之间的组合, 也就是名词短语. 并且可以检测出重合的span. 



### 2. Related work

#### 2.1 About dataset

##### 1) SemEval 17

拥有500篇文章的标注, 其中共有三种实体, Tasks, Methods 和 Materials. 以及两种关系, hyponym-of(上下位关系), syponym-of(对称关系). 

##### 2) SemEval 18 

集中于抽出句子中的实体的关系. 有六个类型的关系类型.

##### 3) New dataset

这是这篇文章提出的dataset, 有以下几个新的特点 :

- 增加了类型种类
- 增加了跨句子间的关系连接
- 移除了一些标注上的限制.



#### 2.2 About method

##### 1) Compared to pipeline processing

将语法分析, 对话关系分析, 指代分析等等前处理都joint学习. 而不是分开, 这个当然是得益于数据集的标注.

##### 2) Compared to joint model

在之前也有过将多者结合在一起joint学习的方案. 但是那些方案有一个限制是, 他们对于entity的识别是有默认的 span boundary 限制. 而在这里移除了这个限制, 可以在所有可能的span中进行选择. 

并且, 说了一个我不懂得的, 借鉴了**end-to-end span-based models for coreference resolution** 和 **semantic role labeling** , 并将其推广到了 multi-task 的框架下. 

##### 3) 总结

有以下几个特点:

- multi-task and joint model
- 移除了 span boundary
- 借鉴了end-to-end span-based models for coreference resolution
- 借鉴了semantic role labeling



### 3. Dataset

#### 3.1 Dataset

- 500 abstracts
- +entity typs
- +relation types
- +cross sentence relations with coreference links

#### 3.2 Annotation Scheme 

- Six entity types:
  - Task, Method, Metric, Material, Other-ScientificTerm and Generic
  - Generic 是当实体被coreference时, 或者包含在一个relation中时才会被这样标注.
- seven relation types
  - Compare, Part-of, Conjunction, Evaluate-for, Feature-of, Used-for, Hyponym-Of
  - 其中只有 Conjunction 和 Compare 是无方向的
- Coreference links
- 关于span
  -  greedy annotation for spans :　倾向于长的span
  - Nested spans are allowed : 可以出现嵌套span

#### 3.3 Human Agreements

反正就是保证数据集标注的不错

#### 3.4 Comparison with previous datasets

<img src="./pictures/1" alt="drawing" width="500"/>

其中的 coref cluster 是指, 所有的指向同一entity的词组被分为一个cluster. 



### 4. Model

#### 4.1 Overview

名字是 SCIIE, 概览图如下:

<img src="./pictures/2" alt="drawing" width="900"/>

#### 4.2 问题设定 

##### 1) Input

- 段落文本 : $D=\{w_1, w_2,...,w_n\}$
- 所有可能的词组组合 : $S=\{s_1,s_2,...,s_N\}$

##### 2) output

- the entity types E for all spans S
  - $L_E$ 代表着所有可能的实体类型
  - 对于每一个span, 都有一个对应的类型, $e_i$
- the relations R for all pair of spans S × S
  - $L_R$代表着所有可能的关系类型
  - 对每一对span, 都有一个对应的类型. $r_{i,j}$
- the coreference links C for all spans in S
  - 对于每一个span都有一个数值对应, $c_i\in\{1,...,i-1,null\} $ for i=1,...,N
  - 也就是说, 指向同一个entity的span会是一样的数值



#### 4.3 Model Definition

- 求条件概率:

  ![](./pictures/3)

- 具体的式子:

  ![](./pictures/4)

  这里的每一个 $\Phi$ 都是一个神经网络. 最后的是个softmax函数求概率.

  详细的模型细节见4.4

- 最后的loss function

  ![](./pictures/5)

#### 4.4 Scoring architecture

##### 1) 五种基本计算单元

- 一元计算单元:

  - $\phi_e(s_i)$ : 判断span $s_i$ 是否是entity e
  - $\phi_{mr}(s_i)$ : 判断span $s_i$ 是否是relation r 中的一元
  - $\phi_{mc}(s_i)$ : 判断span $s_i$ 是否是coreference r 中的一元

- 二元计算单元

  - $\phi_r(s_i,s_j)$ : 判断span $s_i$ 和 $s_j$ 是否是relation r 中的双方.
  - $\phi_c(s_i,s_j)$ : 判断span $s_i$ 和 $s_j$ 是否是coreference r 中的双方.

- 单元的具体形式:

  ![](./pictures/6)

  其中, $x\in\{e,mc,mr\}$,  $y\in\{r,c\}$,

   $g_i$ 是 $s_i$ 的向量. FFNN就是普通的神经网络, 这里用的是BiLSTM.

##### 2) 三个组合计算单元

这里的是最后投入使用的:

![](./pictures/7)

##### 3) 共享向量

这里的span的向量是共享的, 也就达到了 Multi-task 的目的. 



### 5. Knowledge Graph Construction

#### 5.1 构建的方法

就是将从不同abstract抽出的关系构建出小graph后, 将所有的合并, 如下图所示:

![](./pictures/9)

- 关于抽node的方法, 由于一个cluster(指向一个实体的不同span,即拥有coreference)中的span的名称都不一样, 因此这里选择最长的那个作为其名称
- 关于抽relation的方法, 由于在不同abstract中, 两个entity的Relation结果可能不一样, 因此这里采用投票的方法.

#### 5.2 结果

论文用500篇标注的abstract训练出的模型去给110K的abstract进行标注, 听着就不靠谱. 一个很不靠谱的原因是, 看最后的结果:

![](./pictures/8)

可以看到不管是哪一项都是及其不靠谱的.

但是, 论文在第七节给出了一个knowledge graph的评价.

![](./pictures/10)

论文采用采样抽取人工评价的方法进行了评价, 结果显示非常好...好吧, 为什么无法相信. 

