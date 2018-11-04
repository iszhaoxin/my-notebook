# Four methods about Knowledge graph alignment



## *A Joint Embedding Method for Entity Alignment of Knowledge Bases*

### 1. Introduction

中科院的文章, 使用的是 DB-FB 的数据集. 

再次证明做好先行研究是多么重要, 和我的前期基本方法基本上不差, 甚至连数据集的制作也十分相像

这篇文章的目的是知识图谱融合, 针对的是两个拥有相同实体的不同知识图谱, 在只有一部分 Seed Alignment Entities 的情况下, 实现剩余 Alignment Entities 的预测. 



### 2. Contribution

- 制作了数据集
- 提出了一个方法



### 3. Method

#### 3.1 数据集的制作

这里采用的是 DBpedia 和 freebase 的数据集进行对应. 

但是在直接对应之前, 数据集要先把所有的 Compound Value Types 归并成确定的一条 entry. 

> Compound Value Types (CVTs) : 具体注释见 [官网](https://developers.google.com/freebase/guide/basic_concepts)
>
> 也就是说, 有些实体是由多个属性组合而成的
>
> 比如问 中国人口的话, 必须要提供时间. 
>
> 那么在查2010年中国人口的时候, 对于中国这个topic, 就要加两个限制条件, 一个是 "人口" 的 types 和 "年份" 的 type. 从里面挑出2010年对应人口即可. 

接下找到合适的 entities 去爬取数据, 得到结果. 



#### 3.2 Method

基于的是 transE , 并且是一个 jointly 的学习方法:

TransE 的学习方法如下:

![](./pictures/1)

而他提出的模型是加了一个 Entity Alignment 的损失部, 并且对于两个graph中的实体对应方法, 采用的是 线性转换矩阵. 如下:

![](./pictures/2)







## *Multilingual Knowledge Graph Embeddings for Cross-lingual Knowledge Alignment*



### 1. Introduction 

#### 1.1 Motivation

首先这个文章和上面的做的东西大致是一致的, 但是motivation 是不一样的. 

这里注目于 不同语言的 knowledge graph 的 Alignment 问题. 

首先 knowledge graph completion 已经有了众多方法. 但是对于多语种之间的 Cross-lingual Knowledge 还是很欠缺的. 

Cross-lingual Knowledge 的定义为 :

> Matches the monolingual knowledge among various human languages.
>
> 也就是说, Cross-lingual Knowledge base 就是简单的两个语种的实体对应数据库而已. 

#### 1.2 Task

两个语言 entities 的 transition 就是要做的事情. 

这里要做的就是 将两个不同语言的知识图谱之间的 互相对应的 entities 进行连接预测. 

#### 1.3 Dataset 

这里使用的数据集是:

**Monolingual KB**  : WK3l : contains English (En), French (Fr), and German (De) knowledge graphs under DBpedia’s dbo: Person domain. 也就是 DBpedia 的一个子集. 

**Cross-lingual KB :**  ILLs on entities, and multilingual labels of the DBpedia ontology on some relations.

### 2. Method

#### 2.1 Overview

分为两部分, Knowledge model 和 Alignment model. 

##### 1) Knowledge model 

分别的对两个 knowledge graph 进行 embedding. 

这里使用的是 TransE

##### 2) Alignment model

在此之上, Alignment model 要学得两个 knowledge graph embedding space 的转换性质. 

并且, 不仅要学的 entities 的 transition, 还要学会 Relation 的 Transition.

##### 3) 联合起来

将两个模型联合起来. 



#### 2.2 细节

##### 1) Knowledge Model 

对于每个语言的知识图谱, 分别利用 TransE 进行训练:

![](./pictures/3)

##### 2) Alignment Model

###### 1. Align 的 整体框架:

- Seed alignment

  $\delta(L_1,L_2)$  代表种子 Alignment set.

  其中, $L_1,L_2$ 分别代表语言1和语言2. 这个set中的种子数目只不过是 multilingual knowledge base 的一小部分. 

- Alignment 模型

  ![](./pictures/4)

  loss 就是 $S_A$

这里采用了三种 align 的方法:

###### 2. Distance-based Axis Calibration

分了考虑 Relation 和不考虑 Relation 两种情况的, 分别如下:

![](./pictures/7)

![](./pictures/6)

这里注意, 考虑 Relation 的前提是也要有 关于 Relation 对应的 seed 才可以. 

###### 3. **Translation Vectors**

这里把语种间的对应之间当做一个关系去看待. loss如下:

![](./pictures/8)

###### 4. Linear Transformations

这一个方法的假设是, 两个 Embedding space 之间可以通过 seed entity embedding 的线性转换来实现.  同时也分了有Relation和没有的. 

![](./pictures/9)

![](./pictures/10)

##### 3) MTransE

接下来要将两者结合:

$J=S_K + \alpha S_A$

根据选择的 Alignment Model 不同, 一共可以有五个变种. 



### 4. Experiment

#### 4.1 Cross-lingual Entity Matching

这个是对 entity 的对照进行的实验. 并且, 这里采取的方法不是求最佳结果, 而是通过ranking后去 k位 的方式去检测, 也就是 hit. 

除了有 hit@10之外, 还有 mean 的方法, 具体的实现见论文:

> Normalized word embedding and orthogonal transform for bilingual word translation.

结果显示, 无关系的线性转换的方法最好:

![](./pictures/11)



#### 4.2 Triple-wise Alignment Verification

##### 1) Evaluation metric

###### Dissimilarity function

这个是在有 整个triplet对应的情况下进行的实验. 

进行 Evaluation 的方法是:

挑选出所有 完全匹配的 triplets 集的百分之二十作为训练集 ${T,T'}$

$T=\{h,r,t\}, T'=\{h',r',t'\}$ 

> 这里的训练集是用来训练测试的参数, 而不是用于原先的Alignment的训练.

首先利用百分之二十 positive set 去制作 negative set. 

利用 positive set 和 negative set 去分别计算 dissimilarity function :
$$
f_d(T,T') = ||F(h)-h'||_2+||F(r)-r'||_2+||F(t)-t'||_2
$$
这里的 F 就是模型中进行转换后的结果. 

###### Threshold

找到一个 $\sigma$ 使得, $f_d<\sigma$ 的尽可能是positive, 反之是 negative.

##### 2) Result

![](./pictures/12)





## *Cross-Graph Learning of Multi-Relational Associations*



### 1. Introduction

这里所针对的问题和所使用的数据集和上面的论文是一样的. 



### 2. Method

#### 2.1 Overview

分为了两个部分, 一个是 SE, 一个是 AE.

- SE : Structure embedding .

  说是 Structure Embedding, 其实就是一般的 knowledge Graph Embedding 的方法. 这里很明显混淆了一个概念. Structure embedding 和一般的 Node embedding 是不一样的. 

  Structure embedding 是专门的一个对网络中节点拓扑结构进行 Embedding 的方法. 和 Node Embedding 是完全不一样的. 

- AE : Attribution Embedding

  这里增加了 node 的 attribution 信息.  利用这个信息去辅助学习. 

  这里的 assumption 是, 对于某种属性含有相同值的节点更相近. 这个是很好的一个工程方法. 

  > 注意这里使用了 abstract attribution value 的方法. 因为具体的数值没有意义, 因此进行某种程度上的抽象. 

概览图如下:

![](./pictures/14)



#### 2.2 Structure Embedding

这里的 SE 很简单, 就是利用 TransE 去学习一个 unified vector space. 

重要的是, 这里将两个 share entities 看做一个点去对待, 这样的话, 与 share entities 相连的 AB 图的信息就得到了 Alignment. 图示如下:

##### 1. 数据例子:

![](./pictures/13)

其中, washington(in EN) 和 washington(in Fr) 的对应关系是已知的. 而 关系 以及 美国在两个 Graph 中的对应关系是未知的. 

##### 2. 随机状态

下面代表在随机初始化后, 他们之间的状态.  washington(in EN) 和 washington(in Fr) 共享一样的 entity, 所以它只是一个点. 

![](./pictures/15)



##### 3. 学习

![](./pictures/16)

通过学习, 可以尽可能实现下面的 Alignment, 不过实话说, 感觉这个不好办到, 如果 "美国" 在两个数据集中没有其他信息帮助的话, 很有可能就是在两个 "美国" 的点不会重合. 



##### 4. 公式

这里还是用负采样等等基本策略. 公式如下:

![](./pictures/17)

![](./pictures/18)

得到的 $\mathcal{O}_{SE}$ 就是 SE 给出的loss function

但是其最终的loss还不是这个, 而是 margin 形式的 loss function. 

> 关于 loss function 的研究见 [论文笔记]:sampling 

#### 2.3 Attribute Embedding

##### 1. Attribute Embedding 

这里的思想很简单, 对于 an aligned entity pair ($e^1,e^2$) 而言. 

假设其 attribute of $e^1$ is a,  attribute of $e^2$ is c. 那么, 利用语言模型的思想, a和c 蕴藏着correlation 的信息就是 $P(c|a)$. 也就是:

![](./pictures/19)

这个是简单的方式, 我们这里的语言模型不是 n-gram 而是赋予每个属性一个向量. 再运用上 负采样, 最终的损失函数是:

![](./pictures/20)

##### 2. Entity Similarity Calculation 

上面给出了按照 seed aligned pair entities 的信息, 对属性的相关性进行建模的方法. 但是如何利用这个相关性计算 entities 之间的相关性还没有说:

- 平均多个属性的 embedding. 

  一个 entity 会含有多个属性, 因此可以用平均的方式得到这个 node 的 attribute embedding. 即:

  ![](./pictures/21)

  这样两个图中的每个节点都有了一个属性向量. 

- 相似度矩阵

  下面便进行相似度矩阵的定义. 

  - 首先定义相似度用的是 cos 函数:

    ![](./pictures/22)

  - 接下来, 不仅仅是不同图之间的节点有相似度矩阵, 同一图之间的节点也有, 即:

    ![](./pictures/23)

    其中, 

    ![](./pictures/24)

    $n_e^{(1)}, n_e^{(2)}$ 分别是 KB1 和 KB2 的node的数量. 



#### 2.4 Joint Attribute-Preserving Embedding

##### 1. 定义

接下来定义了**利用 attribute Embedding 辅助的 loss function**

![](./pictures/25)

##### 2. 个人分析

- 可以看到, 这里的 attribute embedding 其实更像是一种 heuristic 的辅助信息. 
- 可以类比下[论文笔记]:BIG-ALIGN- Fast Bipartite Graph Alignment 中的 利用 degree distribution 去初始化向量的方法. 
- 个人认为这里的用法不好, 因为属性的应用本身就带有一定的不准确性. 若是像 Big-align一样, 仅仅当做一个初始化的方法后, 继续通过 hard constraint 去学习还好. 但是这里的 S 一旦加上, 就会伴随终生. 虽说这里的 $S^{(1,2)}$ 中属性向量也是在更新的. 感觉有些牵强. 只能当成一个调参的技术. 
- 这里值得注意的一点是, 后面的两项. 这两项的目的是将 $S^{(1)}, S^{(2)}$ 限制为了 单位矩阵. 即, $E_{AE}^{(1)}, E_{AE}^{(2)}$ 成为了 permutation Matrix(见 Big-align) 的笔记.  这里的设定还是挺有意思的. 



##### 3. 最终loss 

![](./pictures/26)

### 3. Discussion

#### 3.1 Training

这里的train过程比较有意思. 虽然是有 joint loss. 但是 $\mathcal{O}_{SE}$ 和  $\mathcal{O}_{S}$ 是分来最小化的. 

这也难怪, 因为其中  $\mathcal{O}_{SE}$ 和  $\mathcal{O}_{S}$ 来自不同的模型. 

#### 3.2 Limition on Embedding

这里其实讨论的是 TransE 中的一个 hard constriant on embedding. 

TransE 规定, node  embedding 的 L2 norm 必须是1. 在这里作者对 attribute Embedding 也加了这样的限制并进行了分析:

原因有三:

- 避免了学习过程中通过增大 embedding norm 来实现一些平衡的方式. 
- 限制住了 randomness of entity and relation distribution. 即 缩小了问题的搜索空间. 
- 适用于 cosine 计算距离. 

便于理解, 可以理解 TransE 将所有 embedding 限制在了一个 高维的球面上, 即 sphere 空间上. 



### 4. Evaluation

终于等来了最后的吐槽. 

实验显示, 仅仅使用 SE 就可以比 MTransE, JE . 天啊, MTransE 好歹也是 IJCAI 的文章, 不给留点面子. 在复现之前不知道说啥,但是感觉不靠谱.

![](./pictures/27)





## *Iterative Entity Alignment via Joint Knowledge Embeddings*



### 1. Introduction

这个也是同样的任务. 

使用的 motivation 和第一个相像. 

使用的数据集是 DBP-1, DBP-2, DBP-3, DBP-4.

是从 FB15K 中制作的 dummy dataset. 

其统计信息如下:

![](./pictures/28)

$O:$ overlap rate, 即两个子图中的重合率.

$\mathbb{L}$ : seed alignment set 的集合

$T_1,T_2$ : 两个子图中的 triplets. 

大概就是从一个 FB15K 中分出两个子图, 然后将这两个子图按照一定量的 seed aligned entities 进行融合. 



### 2. Methods

#### 2.1 Overview

分为三步:

- Knowledge embeddings
- Joint embeddings
- Iterative alignment

损失公式为 : ![](./pictures/29)

这里相比与之前的手法, 加了一步 Iterative alignment. 即, 在训练过程中不断使用已经获取的Alignment entities pair 作为新的 seed 去增强学习. 另外在其他的两步也有小出入.

**概览图如下:**

![](./pictures/30)

#### 2.2 Details

##### 1) Knowledge Embeddings

- 基本思路

  这里做了一个基础方法的改变, 将 TransE 变为了 PTransE. 

  PTransE 的 观点是 : 考虑到了组合关系对 embedding 的影响. 

  即, 不仅仅 $\{h,r,t\}$ 中的 r 对 h 和 t 的预测有影响, $\{h,r_1,e\}, \{e,r_2,t\}$ 中的 $(r_1,r_2)$ 对 h 和 t 的预测也有影响. 

- 实现形式:

  这里在 node embedding 的问题上提出了一个 新的损失函数. 

  - **P函数**

    首先对于 $(r_1,r_2)$ 设定一个 identification 的方式, 这里采用p函数代替:

    $p=f(r_1, r_2)$ . 这里的函数的话参照原论文有各种各样的形式. Mean, product 或是 RNN 都可以. 

    那么对于一个 $\{h,r,t\}$ 中的 $(h,t)$而言,  其可能有多个 $(r_1,r_2)$ 对应, 因此, 这里需要一个集合去定义 h与t 之间的二距关系:
    $$
    P(h,t)=\{p|\forall e\in E,r_1,r_2\in R, (h,r_1,e), (h,r_2,e)\in T, p=f(r_1,r_2)\}
    $$

  - **Triplet loss**

    对于单个 triplet 这里采用了, TransE 一样的方式, 不过加上了远距离(2) 的距离关系, 即:

    ![](./pictures/31) :

    那么对于  $(h,t)$ 中所有可能的 $(r_1,r_2)$ 来说, 就有:

    ![](./pictures/32)

    其中, Z 是 normalization 因子. 

  - **综合 loss**

    再加上一开始 TransE 的单距离loss,  就有了最后的 loss.

    ![](./pictures/33)

##### 2) Joint Embeddings

这里和前面没有什么不一样. 

三种方法:

- **Translation-based Model:**

  在 MTransE 中提到过. $(e_1,e_2)$ 是一个 seed aligned entities pair

  ![](./pictures/34)

- **Linear Transformation Model**:

  在 MTransE 中提到过. 

  ![](./pictures/35)

**基于以上两种方法的 Joint loss 为 :**

![](./pictures/37)

- **Parameter Sharing Model :**

  在 JAPE 中提到过:

  ![](./pictures/36)

  **这个方法的 joint loss 是融合在 $\mathcal{K}_p$ 中的**, 因此这里的 $\mathcal{J}_{T/L}=0$.



##### 3) Iterative Alignment

这里说的是, 如何利用学习中学得的 Alignment 去增强后续学习的方法:

###### 1. 距离计算

通过上面的三个方法, 可以计算出两个图中任意两个 node 之间的 align 距离. 

> 对于前两种方法要加上转换, 后一种方法直接计算距离就好.

这里用 $E(e_1,e_2)$ 来表示两个图中的 node 的距离:

![](./pictures/38)

###### 2. Threshold

对判断 $(e_1,e_2)$ 是不是 Alignment , 这里通过设置阈值的方法去做. 

如果 $E(e_1,e_2) < \theta$. 则在下一轮学习中, 我们将这个 $e_1,e_2$ 作为 seed 去使用. 

###### 3. 使用seed 的方法

这里分了两种, Hard Alignment 和 Soft Alignment.

- **Hard Alignment**

  这里就是直接在新的一轮迭代中将新的 seed 用 前面的方法加入上面的 两种loss计算即可. 即: $\mathcal{J}_S=0$

- **Soft Alignment**

  Hard Alignment 会出现 error propagation 的问题, 因此这里对每个新的 seed 加上了 Reliablity 系数, 使用了 sigmod 函数:

  ![](./pictures/39)

  下面就是正常的加 PTransE 的 loss. 

  ![](./pictures/40)



### 3. Experiment

![](./pictures/41)





