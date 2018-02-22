## BIG-ALIGN- Fast Bipartite Graph Alignment



### 1. 目标

#### 1.1 目标任务

二分图的匹配, 对于现实世界中的含有信息的网络结构而言这是一个重要的课题. 

例如:

1. 两个不同的社交软件中的网络
2. 两个不同的蛋白质信息网络.

除此之外, 对于单个网络而言, 二分网是其常见的结构. 例如

1. user-movie (author-conference 等等) 二分网络 (可以用来进行推荐)

在有一定数据的情况下, 将两个 **二分网** 网络进行matching, 对 二图一 : $G_A(\mathcal{N}_A,\mathcal{E}_A)$分网络中应该连接在一起却没有连接的点 进行matching.

#### 1.2 具体任务

- Formulation : 定义问题
- Algorithm : 解决问题
- Evaluation : 提供评价的方法



### 2. Formulation

#### 2.1 两个图的定义

- $G_A(\mathcal{N}_A,\mathcal{E}_A)$, $G_B(\mathcal{N}_B,\mathcal{E}_B)$
- $A$ 邻接矩阵, $B$ 邻接矩阵

其他的具体的定义:

![](./pictures/1)

#### 2.2 问题设定:

![](./pictures/3)

即, 通过两个 permutation matrix 来实现两个图的 matching. 

##### 1) permutation matrix

这是一个很特殊的矩阵. 拥有一下特点:

1. 只有0和1
2. $AA^T=1$
3. 其行列式的值为 $\pm1$
4. 每行每列之和均为1. 
5. 方阵

#### 2.3 问题设定的缺点 

1. 有两个矩阵需要去学习, 具有 combinatorial 的特性. 因此, 非常难以学习.

2. 对一些问题存在不可解性, 例如, 对于一个完全呈对称卫星状的图结构而言, 这是一个对称结果. 最外层的节点在结构上是**等概率**的. 

   (这里有些不太清楚, 只要有足够多的内部节点信息, 其最外部节点虽说不是可以完全确定, 但还是可以筛掉很多节点才对啊?)

**非常重要的一个点, 图的对称性会对matching产生影响**

因此, 相比较于完全匹配这种 hard assignment 而言, 采用 soft assignment 更适合图结构. 



#### 2.4 soft assignment 

##### 1) 定义

![](./pictures/4)

> 注 : L0-norm : The **L0 norm** is the number of non-zero elements in a vector. 

##### 2) 限制1的解释:

- 针对上面提到的等概率的问题, 利用非 permuation matrix 矩阵来实现概率分配. 
- 由于只需要和为1, 因此也放开了方阵的限制, 使得两个图 A,B 的节点数可以不同.

##### 3) 限制2的解释: 

L0-norm 的限制

由于图是巨大的, 我们只希望对于一个group或者user而言, 在另一个图中只有几个潜在的有可能性的对应的group或者user就好. 因此对其进行了限制. 



### 3. BIG-ALIGN Algorithm

这个算法是相当有数学含量的, 做好心理准备. 

#### 3.1 解决框架

##### 1) 软化 L0-norm

L0-norm 的优化是一个NP难问题, 因此这里采用 L1-norm 去替代 L0-norm. 结果如下:

$||P^{(v)}||_0 \leq t, ||Q^{(v)}||_0 \leq t$  变为了 $\sum_{ij}P_{ij} \leq t, \sum_{ij}Q_{ij} \leq t$

##### 2) 转化矩阵优化问题

上面的 problem2 中的问题形式被转化为如下形式:

![](./pictures/5)

##### 3) 迭代优化 - APGD

使用某种具体的优化方法去实现如下的优化思路:

- 在 P 固定的时候, 去优化 Q
- 在 Q 固定的时候, 去优化 P 

![](./pictures/6)

这个优化思路叫做 **projected gradient descent approach** **APGD**

并且, 在优化过程中, 如果出现小于0 的地方就将其置0.  如果出现小于1 的地方就将其置1. 

##### 4) 针对二分图特性进行修改

上面的分析都是可以针对一般的图结构进行适用的, 但是在这里可以再加上一些限制使其更加适用于二分图结构. 

我们的最终目标是将两个图进行匹配, 但是不是所有的点都有进行匹配的可能性的. 比如, LinkedIn 中的用户只能与 facebook 中的 用户进行匹配, LinkedIn 中的 group 只能与 facebook 中的 group 进行匹配. 因此, 我们的 permutation matrix 的右上和坐下的矩阵是必须为0 的. 即:

![](./pictures/7)

这样, 搜索空间得以缩小.

##### 5) Sum Up

变成了

![](./pictures/8)

当然还有 Problem2 中的限制条件.



#### 3.2 Optimization 

分为三步去做:

- 进行一个好的初始化
- 实施 APGD 优化的方法
- 处理前面提到的发散状网络的等价情况的方法

##### 1) 初始化方法

由于这是一个非凸优化问题, 因此需要进行一个好的初始化, 否则很容易陷入局部最小点.

这里初始化的 insight 是:

**两个图中点的度数一定程度上反映了点的相关性**

即, 两个图中度数高的点之间属于同一个点的可能性要大一些.

这里采用了四个步骤去实现这一点, 但是实际上就是两步. 

###### 1> 按照 degree 分团

初始假设 :

按照两个图 $A, B$ 按照度数分团 分成 ${A_1,...,A_n;B_1,...,B_n}$ 后 , 1-by-1对应起来. 

即, 假设 $A_i$ 中的点 在 B 中的对应尽在 $B_i$ 中. 

分团方法是画出 degree distribution 后, 找 knee(转折点) ? (此处应有黑人问号脸)

找到一个 knee 之后, 按照斜率的比例, 去找下一个knee点. 

我也不太懂这一块, 示意如下:

![](./pictures/9)





###### 2> Align neighbors

上面把团分完之后, 对应团 $A_i, B_i$ 之间的点, 进行相似度匹配. 

公式为:

![](./pictures/10)

> i 是 A 中的点, j 是 B 中的点.

这个计算的其实是, 两个点在两个图中的差异度. 因此, 这里的 rdd 就是指:

**Relative Degree Difference (RDD)**



###### 3> 针对user, group进行初始化

分别针对 user 的 nodes 和, group 的 nodes 进行上述优化.



##### 2) 优化方法

这里的关于优化方法的讨论基本上都是关于超参数 $\eta$ 的讨论. 

这里使用了 line search 的方法

> S. Boyd and L. Vandenberghe. Convex Optimization. Cambridge University Press, New York, NY, USA, 2004.

即:

![](./pictures/11)

这里通过对 line search 的几个不同的使用方法又分出了几个方法.

> 简直就是调参盛典.....(并无褒义)

不写了, 有兴趣看.



##### 3) 处理相同结构点的方法

这里使用整合同结构点的方法. 

例如对于, 一个中心有一个点, 以weight=1连着100个外围点的图来说, 这里的替代方法就是将100个点合成一个 super-node. 以weight=100 连向中心点.

> 越到后面越想吐槽, 一开始感觉不错的说.



##### 3) BIG-ALIGN

最后的算法:

![](./pictures/12)



#### 4. Evaluation

不行了, 不想写了





