## On Inductive Abilities of Latent Factor Models for Relational Learning



### 1. Introduction

#### 1.1 研究对象

知识图谱的完备 : 就是进行基于知识图谱的 link prediction.

从最开始到现在大概有两种模式的模型 : 

- ILP : Inductive logic programming, 具有显式的推理结构.
- Latent factor models, 使用向量去进行推理. 

两者的优缺点也是不言而喻.

从结果来看, Latent factor models具有绝对的优势, 但是这种优势来源于实验科学而不是严谨的科学. 

因此作者为了评价各个模型的inductive 的能力, 设计了几个特殊的实验用来进行对比. 因此这篇文章不是简单的综述, 而是一个着重于评价模型的推导能力的测评文.

#### 1.2 实验设计

- 设计了测试二元关系模型三个基本属性的模型, 这三个属性是 :

  > 二元关系是指, 两个之间只有 [是此关系], [不是此关系]两种结果.

  - 自反性(reflexivity)
  - 对称性(symmetry)
  - 传递性(transitivity)
  - 还有三个属性的互相组合.

  > 这个是对单个关系的属性的检测.

- 设计了一个家庭谱系的推理的真实世界多关系模型.

  在这个实验上, 论文设计了几个不同的训练集和实验集数据的划分方法. 测试了模型对几个基本推理能力的支持能力.

  > 这个是对多个关系之间的推理能力的检测.

- 在此之外还设计了测试模型应归缺失数据的能力.

### 2. Related Work

#### 2.1 领域脉络 

整个领域的脉络如下:

- 所有这一切的基础是 inductive logic programming, 他为我们提供了支持推理能力的工具.

- 其次出现了statistical relational learning field , 在这个领域中, Link Prediction 是其主要的一个应用领域. 

  - 针对 Link Prediction 问题, 早期提出了各种不同的  probabilistic logic-based inference model. 这个时期提出的最强力的模型就是 Markov Logic Networks.

    > Markov Logic Networks:
    >
    > 输入是, 一阶逻辑和facts, 通过facts之间的共起关系建立了一个马尔科夫随机场. 	

  - 2009年, latent factor models 被提出来了. 详细的介绍看上一篇综述笔记.

#### 2.2 Link Prediction模型的各种流派

1. 本文基于上一篇综述进行更深刻的分析, 还另外介绍了holographic embeddings model (等价于 ComplEx model) 和 2012年提出的一个模型被认为是 其他几个模型的混合, 这里将分开解释.

2. **这篇文章不测试的模型.**

   Not all latent models are actually factorization models. 

   这句话的意思是, 有很多基于潜在变量的模型,比如说nerual tensor network(见上个综述笔记). 这些模型不具有分解性, 关于分解的定义见上一篇综述笔记. 

3. **Natural logic operations**

   使用自然语言作为关系符号的模型.  2015年的模型, 使用的是 recurrent neural tensor networks.

4. **编码logic operation为tensor operation的方法**

   上面介绍的方法都是直接从关系数据中习得tensor, 而这个的出发点是 logic operation, 将其转换为tensor的相关操作.

   Basic reasoning with tensor product representation

5. **结合两者的模型**

   两者分别指基于logic的和基于factor的. 下面几个论文结合的方法都是不同的.

   Injecting logical background knowledge into embeddings for relation extraction

   关于rule的误差项被加到Latent model的目标函数中.

   Learning  knowledge  base  inference  with  neural theorem provers

   使用 differentiable neural theore prover去处理两者.

   Adversarial sets for regularising neural link predictors

   使用对抗学习去结合两者.

   Learning first-order logic embeddings via matrix factorization

   学习了, 利用ILP得到的公式所表示的, 一阶逻辑的embedding.

   Embedding entities and relations for learning and inference in knowledge base

   与上面相反, 这篇文章从学得的embedding中总结出了rules, 这个感觉很有意思.

   Translation-based knowledge graph embedding preserving logical property of relation : 设计object和subject的embedding, 使其可以保存关系的传递性和对称性.(不懂)



### 3. The Link-Prediction Task and Models

介绍将要测试的模型. 并且因为有的模型使用了复数, 因此整个评比在复数域下进行.

#### 3.1 Link-Prediction in Knowledge Graphs

关于Link Prediction的定义, 还有一些符号介绍.

定义如下:

![](./pictures/3)



并且, 模型之间的不同其实就体现在他们的目标函数上, 这个在所有领域中都是一样的, 再复杂的神经网络结构, 本质上也就是损失函数不同而已(这里除去优化方法的影响). 下面是各个要测试模型的目标函数: 

![](./pictures/1)

其中, 基于复数域三元运算的定义是:

![](./pictures/2)

#### 3.2 Models

##### 3.2.1 Canonical-Polyadic Decomposition (CP)

1927年论文.

这个里面Entity作为subject和object的时候的embedding是不同的, subject是u,object是v. 

其中Relation Embedding和Entity Embedding的维度都是K.

这个模型中, 同一实体拥有两个embedding的设定使得其难以泛化.

##### 3.2.2 RESCAL

![](./pictures/6)

详见上一篇论文.

##### 3.2.3 F model

![](./pictures/7)

这个里面的 $e_d$ 是 所有可能的实体对, 即:

![](./pictures/8)

W是Relation Embedding矩阵.

这个模型能够处理可观测到的实体对, 但是由于起将一个实体对当做一个整体, 缺少对每一个实体的embedding, 因此无法预测到没有出现过的实体对.

> 在本文的评价中, 对于未出现的实体对的向量采用了高斯初始化.

##### 3.2.4 TransE

![](./pictures/9)

这个是基于距离的模型,但是14年的一个研究显示, 这个还是一个分解模型, 因为:

![](./pictures/10)

这个模型无法支持有对称性的关系, 比如两者相似, 两者相等, 两者为夫妻等等, 也就是说无法在互换subject和object后, 使得分数相等. 即:

![](./pictures/11)

由于

1. 两个不同的实体的embedding肯定是不能相同的
2. 并且关系的向量肯定不能是0

在这种情况下, 对于对称关系,要使上面等式成立的话, 就有一个强的几何约束有: 

3. 两个Entity Embedding垂直于Relation Embedding. 

但是在训练的过程中, 并不会在意这点, 因此最后的结果会是上面三个的trade-off.

##### 3.2.5 DistMult

![](./pictures/12)

与上面相反, 无法支持非对称关系, 因为:

![](./pictures/13)

##### 3.2.6 ComplEX

拥有复数域的DistMult模型.

![](./pictures/14)

每个向量都是一个复数向量, 即: ![](./pictures/15)

其中, $\bar{e}_o$ 是 $e_o$ 的共轭向量. ![](./pictures/16)

通过这种方法, 相当于给每个entity在subject和object上提供了两个向量, 而这两个向量是由共轭复数来体现的, 共轭复数之间天然的相关关系使得模型易于解释. 

是一个很好的, 对 **实体在subject和object上意义不相同但是相关** 这个intuition的建模.

论文对这个模型给予了高度评价.说这个模型可以分解几乎所有的Knowledge Graph. 并且具有线性的空间时间复杂度.

#### 3.3 训练模型.

##### 3.3.1 训练方法

对于关系的预测这里使用的是逻辑函数, 即:

$p(r,s,o;\theta) = \sigma(-y_{rso}\phi(r,s,o;\theta))$

然后使用最大对数似然的方法:

![](./pictures/17)

使用mini-batch的随机梯度下降来进行学习.使用的优化方案是 AdaGrad.

用于 evaluate 的论文的方法是  average precision.

为了进行与Inductive model的比较, 这里还对Inductive model的结果也进行了评价.

在其他的训练设计上与原来的论文有些出入.

##### 3.3.2 训练数据

这里采用的是利用 一阶逻辑规则 合成的数据集.

- 其中一个rule对应着一个Relation, 要进行 genealogical data(家谱数据) 上的推理能力测试. 这里的 genealogical data 虽然不清楚, 但是应该就是rules之间具有确定性的相关关系的关系数据吧, 比如说妈妈的妈妈是祖母这种感觉的数据.
- 随机drop关系, 测试对missing data的应对能力, 这里就强调了面对现实数据的能力, 比如在数据库中, 可能并不是所有人的妈妈的妈妈和他本身之间都有 [祖母-孙子] 关系的连接.

并且, 这里的数据很简单, 并且是确定性, 即rule之间的关系是确定性的, 就像上面的例子. 

不确定的数据是指, 我在上一篇综述笔记中提到的软式连接, 比如, A擅长做寿司, 很有可能A是日本人, 但是并不代表A一定是日本人.



### 4. 对关系三个基本性质的测试

解释了二元关系的三个性质, 然后设计了分别检测他们三个和他们之间组合的实验, 并展示了结果.

#### 4.1 实验设计

每种关系都可以去判断有没有这三种性质. 

$r(s,o)$ 的结果只能是 True or False.

##### 4.1.1 三个性质的解释

以偏序关系为例:

- 自反性(reflexivity) : a ≤ a , 即这个关系用于两个一样的实体的话,结果一定是True.

  与此相对的 irreflexivity : 例如真子集关系. 即, 关系用于两个一样的实体的话,结果一定是False.

- 对称性(symmetry) : 相似关系,两个离散的元素之间的关系的方向可以颠倒. $r(s,o)=r(o,s)$

  与此相对的是 antisymmetry 例如偏序关系中的 if a ≤ b and b ≤ a, then a = b

- 传递性(transitivity) : if a ≤ b and b ≤ c, then a ≤ c. 这个很好理解了.

  与此相对的是 无transitivity : 连接关系, 若A和B相连, B和C相连, A和C不一定相连.

更多的例子:

![](./pictures/18)

##### 4.1.2 模型需要拥有能力 

**基本模型设置**

因为在现实世界的关系中, 这三种能力都是会出现的, 因此模型必须要有处理这三种性质的能力.

关系可能有的性质有,  (reflexivity, irrreflexivity, neither)\*(symmetry, antisymmetry, neither)\*(is_transitivity, not_transitivity) = 18 种可能性, 除去五种不感兴趣的, 有13个.

论文设计了13中算法去随机生成50\*50的矩阵,也就是说一共50个实体. 每个类型可能生成几个矩阵,或者一个, 论文没说. 因为处理的是multi-Relation. 其中每个矩阵代表了一个关系.

reflexivity : 针对 reflexivity 的话, 就只需要对矩阵的对角线进行定义就好, reflexivity就是全为1, irreflexivity是全为-1, neither是两者兼有.

symmetry : 就是对称矩阵, antisymmetry就是不能是对称元素相反的矩阵. neither就是不满足上述两个情况的矩阵.

transitivity :就需要用到一些约束条件去生成矩阵了.

**Individual 和 Joint**

在 Individual 的模型设置中, 针对每一个关系进行建模. 输出可用sigmod函数.

Train/test/Valid:8:1:1

在 Joint 的模型设置中, 针对所有的关系一起进行建模. 输出可用softmax函数. 

Train/test/Valid 的比例变化, 通过这种方法,来模拟**missing data**的情况.

#### 4.2 Results

##### 4.2.1 Individual

###### Reflexivity

以Reflexity为自变量, 对数据设置对照组, 结果显示对照组中的两组的结果相同.

说明, Reflexivity性对结果没有影响. 因此不考虑它.

###### Symmetry

**结果**

以Symmetry与AntiSymmetry的数据作为对照组, 结果如下:

![](./pictures/19)

横轴是指分解之后的rank, , 竖轴是预测精确度. 这里的预测是指. 对矩阵中空白位置的预测.

>  这里都是分解模型, 就是将原先的矩阵分解为几个子矩阵或者向量的乘积, 这里的rank就是分解后的向量或矩阵的rank

可以看出, 只有 ComPlex和 RESCAL 支持两者.

**分析**

CP : 由于subject和object是无关的, 因此无法表示

DISTMULT : 由于本身计算就不存在正反, 所以不支持

TranSE : 3.2 节讨论了

F model : 在Individual的情况下无法训练, 因为新的实体对没有任何的可参照信息.

TransE : 虽然有能力处理两种情况, 但是和 ComPLex相比 , 随着rank的上升, 效果开始变差, 这是因为 TransE 中有一个二维矩阵, 参数量急剧上升, 导致泛化能力不足.

###### Transitive

由于not_transitive的情况就是没有的情况, 因此不需要设置对照组, 只需要看模型有没有支持transitive的能力即可. 结果显示基本上都很好的支持了. 如下图:

![](./pictures/20)

还加了和对称非对称的组合:

![](./pictures/21)

##### 4.2.2 Joint

直接看图:

![](./pictures/22)

注意到这个里面的 logic 一直很高, 是因为这个就是根据 logic 制定的数据, 而现实永远没有这么简单. 并且,这里的joint的实验有五个, 分别是...(看图注释).

也不需要说啥了, 直接上结论. 

- 随着缺失数据的增加, logic模型与Latent模型的差距逐渐加大. 因此当数据缺失时, 还可以做很多事情.
- ComPlex 最强.
- RESCAL 应对missing的能力最强, 有可能是因为他的多参数是他有较强的表现能力. (但是与此同时他的泛化能力应该差才对啊?)



### 5. Inter-Relational Patterns

下面开始对多个关系之间的组合模式之间隐藏的推理信息进行测试.

家谱数据就真的只是家谱数据. 如下图:

![](./pictures/23)

#### 5.1 实验设计

有点复杂.

##### 5.1.1 基本关系

家庭关系中, 有很多关系都是可以根据其他关系来推出来的, 具体见下表:

![](./pictures/24)

这其中, 有四个是基本关系, 其他13个是可推导关系.

##### 5.1.2 数据组成

一共抽取了5个家庭, 每个家庭有23个人, 加上一个17个关系, 那么总共就有 23\*23\*17 个facts(包含False). 五个家庭之间互不相连.

加上上面的 **按基本关系划分** , 和这里的 **按家庭划分** 的都可以用下面的图表示:

![](./pictures/25)

##### 5.1.3 三组实验

**随机抽除**, **只抽除非必要关系**, **按家庭抽除**, 

具体见下图: 蓝色表示可以抽除的范围:

![](./pictures/26)

然后, 还通过设置P, 即抽除率来模拟对missing data的能力.



#### 5.2 实验结果

实验太多, 只放图.

##### 5.2.1 随机抽除(Random Split)

![](./pictures/27)

##### 5.2.2 **只抽除非必要关系(Evidence Split)** 

![](./pictures/28)

##### 5.2.3 **按家庭抽除**(Family Split) 

![](./pictures/29)

##### 5.2.4 结果

1. RESCAL 效果最好, 但是随着K变大出现过拟合.
2. RESCAL和TranSE应对missing data 能力最强
3. ComPlex 在数据较多的情况下表现好, 并且很难过拟合.

4.  当training set和test set中的Relation的分布不同是, 会造成比较大的问题. 但是可以简单通过对Relation加权来平衡各Relation的比例来轻松解决.
5.  Entity Embedding之间参数共享对于提升disjoint sets of entities 之间的知识转移很重要. 对应最后一组实验. 而这里的Latent 模型使用的都是隐式的parameter sharing. (关于显式隐式的区别见 [基于神经图网络的知识图谱补全] 的论文笔记.)



### 6. Future Research Directions
| Model\Ability | 支持所有性质 | 应对多实体能力 | 应对缺失数据能力 | Train/Test未知分布 | 对新sub-graph支持能力 |
| ------------- | ------------- | ---- | ---- | ---- | ---- |
| ComPlex  | 有&强 | 强 | 弱 | 弱 | 弱 |
| RESCAL  | 有&弱 | 弱 | 强 | 弱 | 强 |
| TransE | 无 | 强 | 强 | 强 | 弱 |


1. RESCAL和ComPlex 支持所有的性质
2. ComPlex拥有稳定的泛化能力.
3. RESCAL面对很多的关系时表现出的效果太差.
4. F-model 对于non-compositional pairs of entities 性能好(不懂)
5. ComPlex在面对较多missing data的时候的能力是一个方向.
6. RESCAL和TransE模型, 在应对较多missing data上, 能力强. 这个是由于 TransE的双线性和RESCAL的多参数.

因此可以将各种模型组合:

1. Complex和RESCAL

   给ComPlex更多的表达能力, RESCAL表示Relation Embedding用的是矩阵, 参数太多, 可以设计一个位于K和K^2之间的参数方案, 例如tridiagonal symmetric matrices, 如下图:

   ![](./pictures/30)

2. TransE+F+Complex

问题

1. 在不同的Knowledge Graph(disjoint set of entities)中的知识转移效果差, 例如最后一组实验中, P=0的时候所展示的. 

   一个解决方向是, Entity Embedding之间共享参数, 2009年一个研究使用了贝叶斯聚类的分解模型 : Modelling relational data using bayesian clustered tensor factorization. 

   还有就是 : Nested factorization.

2. 在面对未知实体上的能力. 又出现了hamaguchi的文章 : Kowledge transfer for out-of-knowledge-base entities:  A graph neural network approach.

3. 在trainset和test set分布不同时, 效果不好, 对应Evidence组实验. 可以通过Relation Embedding之间的参数共享解决.



