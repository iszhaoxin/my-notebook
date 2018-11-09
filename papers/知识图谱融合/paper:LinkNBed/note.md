# LinkNBed

**LinkNBed: Multi-Graph Representation Learning with Entity Linkage**

### 1. Introduction

#### 1.1 Motivation

- 知识图谱在各种应用中都很重要
- 所有知识图谱都有 不完整 (incompleteness) 以及 稀疏 (sparsity) 的问题.
- 虽说有一些方法去做这些, 但是都是从一个图中学习的, 没有利用 overlapping entities 的信息去做改进的model.

#### 1.2 Related work

有一些关于 Graph Alignment 的先行研究. 在其他论文笔记中会写. 

#### 1.3 Contribution

1. 最基础的 jointly 学习 node embedding 以及 Alignment. 
2. 提出了一个可以捕捉实体和关系的上下文信息的通用框架. 
3. 提出了在 **无label(seed Alignment)** 的情况 以及只有 **negative sampling 的情况下** 学习的方法. 这个承认有些牛.
4. 提升优化速率为线性于 triplet 的数目. 

#### 1.4 Dataset

使用数据集为 IMDB 以及 Freebase. 



### 2. Method

#### 2.1 Insight

1. Embedding similarity : 

   拥有同一实体的向量应该相近, 这个在之前介绍的论文中也有出现. 

2. Semantic Replacement :

   不仅仅只看两个单独的 entity Embedding 要相近, 将一个 entity $e^s$ 的对应的 entity $e^{s'}$ 的向量 换到 $e^s$ 的地方后, 其关于 $e^s$ triplet 的 semantic 信息 (Relational score) 也应该是相似的. 即:

   ![](./pictures/1)



#### 2.2 Overview 

![](./pictures/2)

这里的 score function 想去模拟的是一个 triplet 是真实的可信度. 

之后才会利用上面说的两个 insight 去编入 Embedding similarity 信息 和 Semantic Replacement 信息. 

#### 2.3 Triplet embedding

##### 2.3.1 Atomic view

###### 1) 四种 embedding

Entity embedding 和 Relation embedding 说的就是一般意义上的 embedding, 但是 这里还附加了 Attribute 以及 Type 的 embedding, 分别是他们 entity 以及 Relation 的附加信息. 

###### 2) 转换公式 - 关于Entities, Relations and Types

这里讲的是如何从 one-hot 转换为 embedding, 就是一个 EmbedID 函数需要解决的事情 .... 

![](./pictures/3)

###### 3) 转换公式 - 关于 Attribute

这里的 Attribute 是每个 entity 的附加信息, 多是文字的形式, 因此这里利用 paragraph2vec 的方法去实现这里的 embedding. 即:

![](./pictures/4)

##### 2.3.2 Contextual Layer

到了这一层, 其实还是在对每个 entity 以及 Relation 进行 embed 的阶段. 这里要做的就是利用entity 以及 Relation 的临近节点的信息, 对其进行 embedding. 

###### 1) Notation 

$z$ : 代表的既可以是 entity 也可以是 Relation. 

$\mathbb{z}$ : 代表的既可以是 entity 也可以是 Relation. 

$C(z)$ : 代表了 z 的邻接 entity/relation. 

$\mathbb{c}(z)$ : 代表聚合了邻接 vector embedding 的信息的 z 的 embedding. 

###### 2) 信息聚合模型

**这里其实相当于 GNN 里的聚合过程. 聚合起周围的信息**

一般形式为:

![](./pictures/6)

加上 Attention 的信息为:

![](./pictures/7)

###### 3) 具体细节

看图中可以知道 一共使用三种 信息聚合模型. 

- $N_c(e)$ :  Entity Neighborhood Context

  聚合模型采用的是 mean 函数, 而context 信息是 entity 周围的 entity embedding. 

  ![](./pictures/8)

- $A_c(e)$ : Entity Attribute Context

  聚合模型采用的是 max 函数, 而context 信息是 entity e 所有的 attribute 信息(注意这里没有用邻居的信息)

  ![](./pictures/9)

- $T_c(r)$ : Relation Type Context

  这里使用的不是邻接边的信息, 而是 Relation r 的邻接节点的 type 信息. 

  ![](./pictures/11)

##### 2.3.3 Representation Layer

上面是将周围的信息聚集起来了, 但是还没有将 attribute 以及 entity 和 Type 以及 Relation 的信息聚集起来, 这一步就是做这个的:

![](./pictures/12)

最后得到了 triplet $e^s,r,e^o$ 的三个 embedding. 



##### 2.3.4 Relational Score Function

这里计算 triplet 的可信度. 

这里采用的 insight 是 RESCAL 的那一套. 也就是说, 使用的 乘积作为转换方式, 而非欧式空间的距离(TransE-style)

![](./pictures/13)



#### 2.4 Objective function

上面讲的是如何 embedding triplet 的可信度, 下面就开始将 triplet 的 embedding 应用于 2.1 的两个 insight. 

##### 2.4.1 Relational Learning Loss

就是一般的 margin-based loss

![](./pictures/14)

##### 2.4.2 Linkage Learning Loss

将 triplet 中的 一个 e 替换后, 在进行上面的 margin-based loss 计算:

![](./pictures/15)

##### 2.4.3 综合loss

![](./pictures/16)



### 3. Related Work

这里还是比较全面的列出了一些相关研究, 感觉比较好, 这里记录下:

#### 3.1 Neural Embedding Methods for Relational Learning

这里主要是将 KBC 的研究, 这个有很多了, 也做过很多笔记了就不多说了. 简单提几个有趣的. 

##### 1) Compositional Models

代表的就是 RESCAL, 最好的是 Complex 以及 Analogy .

##### 2) Translational Models

TransE 系列.

#### 3.2 Entity Resolution in Relational Data

这是一个全新的思路

![](./pictures/17)

对应的任务和 entity linkage 其实是很相像的. 这里有很多线性研究:

- 2010 : Entity disambiguation for knowledge base population

  最开始的研究. 

- 2013 : Learning entity representation for entity disambiguation. 

  learns entity embeddings for resolution

- 2015 : Leveraging deep neural networks and knowledge graphs for entity disambiguation.

  提出了 使用 sophisticated DNN architecture 的模型. 

- 2016 : Cross-domain entity resolution in social media.

  各个 社交 media 上的 entity 消歧.

- 2016 : Entity disambiguation by knowledge and text jointly embedding.

  使用 text 去辅助 Graph entity resolution. 

- 2016 : Collective entity resolution with multi-focal attention

  使用了 Attention mechanism. 

#### 3.3 Learning across multiple graphs

就是和这个任务目标相同的研究

- Liu and Yang, 2016 : 

  Cross-graph learning of multi-relational associations

  使用一个图, 将其分开后研究其关系, 但是没有使用多个图. 


- Holistic entity matching across knowledge graphs

  基于logic的


- Pershina et al.,2015:

  Pairwise entity matching across multi- relational graphs and is very expensive

  确实成对的学习了 entity matching, 但是 代价昂贵.

- Chen et al.,2017 : MtransE

- Big-Align : 太多 ticky. 