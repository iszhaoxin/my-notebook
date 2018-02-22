## A Comprehensive Survey of Graph Embedding: Problems, Techniques and Applications



### 1. Introduction

#### 1.1 Graph Representation

1. 对每个node进行编码 : 基于流学习 4.1 节(早期方法)
2. 对graph的子部分进行编码 (对应下图bd)
   - 基于深度学习 4.2
   - 基 于对计算「edge重建概率目标函数」的设计 4.3 
3. 对graph整体进行编码 : 4.4(对应下图e)

![](./pictures/1)

#### 1.2 Graph Embedding

##### 1.2.1 Graph Embedding != Graph Representation 

相关的两个问题

- Graph Representation : 表示Graph.
- Graph Analytics : 从Graph中抽取信息

Graph Embedding可以解决上面两个问题. 即 **在表示的过程中包含了graph的信息**

这个**包含信息**的方法是, 将Graph表示为 低维向量(s), 与此相对, **无法包含信息**的Representation方法也有, 比如one-hot向量, 它就只是表示, 而无法**包含信息**

##### 1.2.2 Graph Embedding的种类

按照input输入:

- Homogeneous graph : 同构网络, 网络中的节点没有进行类型标注.
- Heterogeneous graph : 异质网络, 网络中的节点有不同的类型属性.
- 外部信息+graph : 比如关系抽取.
- 非结构性数据(中构建Graph)

按照output分类:

- node embedding
- edge embedding
- 混合embedding
- 整个Graph的embedding.

#### 1.3 分类方法

按照两种分类规则进行介绍, 并非上面的两种, 而是

- 根据问题设置的文献分类 : 这里在安装1.2.2 节进行分类. (**本文独到**)
- 按照使用技术的分类 : 这里按照使用的手法的insight进行分类.(**本文独到是解释每个方法的本质**)

见下图:

![](./pictures/2)

#### 1.4 本文贡献

- 从问题设定和方法两个方向进行survey
- 对使用的方法进行了详细的本质上的分析
- 对使用Graph Embedding的研究进行了介绍
- 从四个方向提出了未来的研究方向 :  计算效率的提升, 问题设定, 解决方案,  applications

#### 1.5 本文架构

2. 介绍定义
3. 在问题设定上的介绍
4. 在解决方案上的介绍.
5. 应用介绍
6. 未来方向



### 2. Problem Formalization

定义如下:

![](./pictures/3)

![](./pictures/4)

![](./pictures/5)

![](./pictures/6)

![](./pictures/7)

这里着重说一下, 一阶近似和二阶近似.

- 首先这里的近似是指, 两个点之间的关联度.

- n阶近似的计算方法 : $s_{ij}^{n}$

  $s_{ij}^{n}=f(s_{i}^{n-1},s_{j}^{n-1})$

  其中, $s_{i}^{n}=[s_{i1}^n,s_{i2}^n,...,s_{ik}^n]$,  这个维度就是node数减一. $f$ 是相似函数.

- 一阶中,  $s_{ij}^1$ 就是两个点之间的weight. 就是说, 一个node的信息只来源于他的邻居;而二阶近似说的是, node的信息来源于他的邻居的邻居.

###  3. PROBLEM SETTINGS OF GRAPH EMBEDDING

本节不介绍, 就是讲了几种不同的输入和输出, 空想都想得到, 这写了三页.



### 4.  GRAPH EMBEDDING TECHNIQUES

#### 4.1 方法概览

![](./pictures/8)

#### 4.2 Matrix Factorization

矩阵分解方法, 这里的通常处理的是**由特性进行总结出来的图结构数据,即No-Relation data**, 也就是说图中的点都可以分解为多个属性值的组合. 比如说在另外的笔记中提到的演员的例子. 

因为这个下面的方法中, 提供了自己设计距离函数来获得邻接矩阵的方法, 当然对于一般的Graph结构也可以使用. 

##### 4.2.1 Graph Laplacian Eigenmaps

- **Insight**

  如果两个相似的点的Embedding相距较远的话, 会给损失函数大的惩罚.

  用的是图切分的思想, 即尽可能使两个被切分出去的子图之间的连接尽可能少, 而使子图内部的连接尽可能多. 在优化过程中可以获得相近的词向量. 简单来说是: 

  用到的是谱聚类算法, 具体见[博客](https://www.cnblogs.com/pinard/p/6221564.html) 以及我基于这个的自己的总结笔记[谱聚类].

  简单来说,谱聚类是以图切分为目的, 最后求出了一般的Node Embedding, 然后又通过K-means进行聚类的过程, 在这里其实可以只用到前一部分.

- **具体过程**

  Input

  1. 一个点集 $E$, 每个点有一定的特征. 这里的特征可以是各式各样的, 例如:

     1) 对于高维空间上的聚类问题 : 特征可以是空间位置

     2) 对于图的切分问题 : 特征可以是与其他点的连接的权重.

     3) 对于知识图谱 : 每个点的特征与其他点的连接 : $\{(e,r_i,e_j)\}_{i\in R,j\in E}$, 也可以是从文本中提取的特征

  2. 一个计算点之间距离的函数 $\to W$.

     这个可以采用多种方式, 既可以直接利用input中已有的边数据(如果给出的话). 也可以利用KNN, 高斯核等等的方法. 利用这个函数和上面的点集可以计算出邻接矩阵 $W$

  3. 目标函数(模型)

     1) 基本函数: ![](./pictures/9)

     2) 改进 : 上面的这个模型只能处理出现过的点, 针对没有出现的这里采用了特征向量的思想, 即, $y=X^Ta$ . 其中, a 是关于这个点的特征(这个必须给出) . 那么对于一个新的点, 因为知道的它的特征, 再乘以训练得出来的矩阵 $X$ 就好. 即将y替换为 $X^Ta$. 目标函数为:

     ![](./pictures/10)

     3) 其他等等等等

  Output

  node向量.

- **各种各样的模型**

  就是在计算距离的函数和目标函数的不同上. 如下图:

  ![](./pictures/11)

  1. 其中W一列中有KNN的行就是使用KNN进行距离计算的方法.
  2. 在由外部信息可以加入的时候, 多是在距离函数上的改进.



##### 4.2.2 Node proximity Matrix Factorization

- **Insight**

  利用W的矩阵分解得到Node的近似.即:

  ![](./pictures/12)

  认为node向量的信息可以从邻接矩阵中分解出来, 这里的邻接矩阵和4.2.1中一样, 可以由各种各样的方法获得.

  这里的 $Y$ 是node的Embedding, $Y^c$ 含有该node的相邻nodes的信息Embedding.

- **方法**

  比较流行的就是用SVD.

  最后获得的Node Embedding既可以是 $y=Y$, 也可以是 $y=[Y;Y^c]$, 即两者的毗连.

- **各种各样的模型**

  ![](./pictures/13)



#### 4.3 Deep Learning

##### 4.3.1 DL based Graph Embedding with Random Walk

- **Insight**

  基于随机行走的模型, 这个模型和语言模型具有相似之处, 都是通过已经通过的点去预测下一个点, 通过这样的方式, 来使可以通过行走到达的点具有相似的Embedding.

- **方法**

  这个下面的方法都比较简单, 最初的模型是DeepWalk模型. 

  > 其实DeepWalk模型的本质还是PMI, 这个在语言模型已经被证明了, 详情见[论文笔记]Neural Word Embedding as Implicit Matrix Factorization.
  >
  > 还有上面4.2.2中提到的模型 TADW 其实本质和DeepWalk是一样的, 就是使用了外部文本特征信息而已.

  公式为:

  ![](./pictures/14)

  ![](./pictures/15)

  和语言模型一模一样, 这里还介绍了 Hierarchical Softmax 和 Negative Sampling的技术, 都是Word2vec中的老东西. 详情见[博客笔记]Word2vec.

- **模型分类方法**

  **行走方式**

  按照行走方式可以将模型分开. 主流用的是截断随机行走, 就是在随机行走的基础上加上一个截断值, 超过这个值就停止, 相当于n-gram中的n.

  除此之外还有深度优先和广度优先结合的研究方法. 以及根据固定结构进行采样行走的方法, 还有基于属性值的行走方法, 都是简单可以想得到的东西.

  **使用网络模型**

  而在网络上, 除了简单的SkipGram之外, 还有使用LSTM和GRU的, 基本上和语言模型的思路没有什么两样.

- **模型分类**

  ![](./pictures/16)

##### 4.3.2 DL based Graph Embedding without Random Walk

- **Insight**

  思想最为简单, 利用深度模型处理Graph数据.

- **几种方法**

  1. 利用自编码器对Graph进行编码, 损失函数基于数据的重建.
  2. DNN, 主要是CNN及其变种(在欧几里得空间上的卷积), 还有GNN(在非欧几里得空间的卷积). 
  3. 其他

  > 损失函数各种各样.

- **各种各样模型**

  ![](./pictures/17)



#### 4.4 Edge Reconstruction based Optimization

**Overall Insight**

边是根据其两边的点预测而来的, 因此对于Link Prediction问题, 新的node pair的edge预测结果, 应该与训练数据中相同.

> 其实我感觉作者的分类不太好, 这里的Edge Reconstruction, 和上面的DNN的方法其实并不是两类, DNN是处理graph数据的方法,同时也制定了优化的方法. 而 Edge Construction 是定义了损失函数. 就相当于, Edge Construction是引擎, 是进行优化的根本来源. 而DNN是车的机械部分, 将引擎的力量带给全车进行运动. 

##### 4.4.1 Maximizing Edge Reconstruction Probability

- **Insight**

  根据好的node embedding去预测nodes之间edge的概率大.

- **具体**

  预测函数是(最简单的例子) :

  只利用一阶近似信息 : ![](./pictures/18)

  关于整个Graph的目标函数 : ![](./pictures/20)
  利用二阶近似信息 : ![](./pictures/19)
  关于整个Graph的目标函数 : ![](./pictures/21)

##### 4.4.2  Minimizing Distance-based Loss

- **Insight**

  根据好的node embedding去预测nodes之间edge的概率与真实的概率分布之间的KL距离小.

  > 这个乍一看和4.4.1 有点像, 但是实际还是不一样的, 一个是最大化现有数据的概率, 一个是最小化预测分布与真实分布之间的距离. 但是到底哪不一样呢? 好奇怪.

- **具体公式**

  还是分了一阶二阶, 预测分布是不变的.就是目标函数变了, 并且增加了真实分布的计算函数.

  一阶真实分布的计算函数 : 
  ![](./pictures/22)
  一阶目标函数 : 
  ![](./pictures/23)
  二阶目标函数 : 
  ![](./pictures/25)

- **各种各样的模型**

  这里的模型也包括4.4.1节的.

  ![](./pictures/27)

##### 4.4.3 Minimizing Margin-based Ranking Loss

- **Insight**

  拥有Relation的nodes之间的相似度应该大于不相关的nodes.

- **目标函数**

  ![](./pictures/26)

  目标是拉大正样本和负样本之间nodes相似度的差距, 使得其接近甚至超越 $\gamma$ . 通过这种方法达到Insight.

- **优化**

  我在4.4的一开始吐槽了作者的分类, 作者在这里没有讨论讲两者结合的方案, 例如, 我们研究室前辈的那篇.  那么在这里的优化, 都是设置的最简单的随机梯度下降法, 直接在目标函数上优化..

- **Knowledge Graph Completion**

  在Knowledge Graph Completion的问题上, 基本上所有的模型都是采用的 Margin-based 的方法.  并且, 由于Knowledge Graph Completion中都要求对Relation也进行编码, 因此去将Relation Embedding加入目标函数中, 就成了下面的格式:

  ![](./pictures/28)

  下面是各种各样的模型:

  ![](./pictures/29)

  这个里面的一个很厉害的模型是ComPlex, 详情见Inductive的那篇论文笔记.



#### 4.5 Graph Kernel

不敢兴趣, 忽略.



#### 4.6 Generative Model

生成模型就是指, 输入不是完全可信的. 需要对输入一起学习. 采用的方法, 学习一个输入特征和class label的联合分布. 比如说 LDA, 这个里面文档被视作基于topic的分布.即: $p(doc|topic)=f$. 而topic是基于单词的分布, 即: $p(topic|words)=f'$

##### 4.6.1 Embed Graph Into The Latent Semantic Space

- **Insight**

  模型认为, node和node之间的关系, 这个三元组决定了图的结构. 而最重要的信息隐藏在这三者之下. 即有个隐藏变量. 

  把图映射到一个潜在的语义空间, 这个语义空间类似于LDA中的(word, topic, docu)三元组组成的空间. 其本质公式如下

  $p(doc|topic)=f, p(topic|words)=f' \to p(edge|latent)=f,p(latent|node)=f'$

- **解释**

  文中类比了LDA模型, LDA模型中, 一开始只要document. 在document中有单词. 我们可以把word当做node, word在不同document中的共现当做他们之间的连接. 而这个连接是和某个隐含变量有关系的. 也就是对应了LDA中的 topic.

  这个模型有点像[Variational KG reasoning](https://zhuanlan.zhihu.com/p/39520002)里面的模型, 但并不一样. 这个模型中直接定义了这个隐含变量 - 为两个点之间基于随机行走的其他路径, 并尝试对其进行编码. 



##### 4.6.2 Incorporate latent Semantic for Graph Embedding.

把潜在语义整合到Graph Embedding中, 可以看到和上面是相反的.

- **Insight**

  拥有相似语义的node应该具有相似的 Node Embedding. 而node的语义可以从node的描述中通过一个生成模型生成.

- **解释**

  说实话这个方法看不懂, 接下来看其中几个具体的文章.

  - Probabilistic latent document network embedding
  - Transg : A generative model for knowledge graph embedding
  - SSP: semantic space projection for knowledge graph embedding with text  descriptions