### 概率, 图以及因果模型的入门



### 1. 概率论的基本概念

#### 1.1 基本概念

- 从贝叶斯角度理解概率是本书的重点
- 概率研究的是, 对 events 发生的degrees of belief. 




### 2. Graph and probabilities

这一小节讲了与因果图密不可分的概率图知识的基础部分. 是非常重要的. 并且里面有很多和后面很相近的概念, 必须要完全把握这一节, 才可以对后面的知识有一个更正确的认识. 

#### 2.1 Graphical Notation and Terminology

概率图的术语

- Graph  : (省略)

- Bidirected (双向边) : 就是两个点之间互相有指向对方的边. 这样的边用虚的有方向的线表示. 

  (这个边在因果论中被称为 confounder). 

- Directed graphs (有向图) : 可以有双向边, 但是不能有 self-loop

- Directed cycles (有向圈) : 具有双向边结构的图 , e.g (X->Y, Y->X)

- Acyclic (无环图) : 不具有 双向边结构 的图

- **Directed acyclic graph ( DAG 有向无环图)** : 即有向又无环的图, 这个是本书中最重要的概念. 

关于图中点的关系:

- Parents, children, descendants, ancestors : 按照家族关系可以推理出
- Spouses : 指向同一个子节点的父节点互为 spouses
- Sink (汇点) : 没有子节点的点
- Root (根节点) : 没有父节点的点
- Tree (树) : 所有点都只有最多一个节点的图叫做树
- Chain (链) : 树中, 所有点最多只有一个子节点的图叫做链. 
- Complete (完全图) : 图中所有节点之间互相都有连接的图叫完全图. 

#### 2.2 Bayesian Networks

上面其实只是对图论中的一些概念进行了总结, 下面我们要对贝叶斯网络, 即将图论引入概率论的方法进行介绍. 

##### 1) 为什么要贝叶斯网络？

真实需要模拟的分布, 通常含有大量变量以及变量之间含有各种各样的依赖关系. 只是用概率分布去列举这些分布的话, 会非常吃力, 因此利用图对变量之间的关系进行建模. 

也就是说, 将一个非常巨大的联合分布, 分解为一个个小的边缘分布, 用图中的一条条边代替. 

##### 2) 表示方法

一般用有向图去表示贝叶斯网络, 主要就是 DAG

##### 3) 建模

首先将联合分布分成条件分布的乘积. 

![](./pictures/15)

其次, 搞清楚每个变量和其它变量的依赖关系. 其中 $pa_j$ 代表的是 $x_j$ 的祖先. 

![](./pictures/16)

##### 4) Markovian Parents

此处有一个 Markovian Parents 的定义 : 

![](./pictures/17)



##### 5) 顺序的重要性

根据观测数据去制作联合分布的概率图的方法如下:

- 先给所有变量一个顺序, $(x_1, ... , x_n)$
- 然后按照这个顺序对变量组 $(x_1, ..., x_i)$ 的相关性进行检测
- 按照概率图的方法, 对 $x_{i+1}$ 变量关于之前的变量进行条件独立的相关性检验. 

问题所在 :

可以看到, 在这个过程中, 初始变量的排列顺序对后面的图的生成产生了至关重要的影响. 

因此, **一个联合分布可能会对应着很多个贝叶斯网络**



##### 6) Markov Compatibility 

**马尔科夫互换性**

![](./pictures/18)

如果一个联合分布 P 可以用 一个 DAG G 去表示, 那么就说 P和G 是**马尔科夫相关** .

> 那么, 按照上面的说法, 与一个 P 有马尔科夫相关关系的 G 也许会有很多个





#### 2.3 The d-Separation Criterion

这个是属于概率图论中, 对有向概率图中的节点的独立关系进行判断的一个方法. 

##### 1) d-separataion 

关于如何分离的方法, 建议看 PRML 第八章, 那里讲的很清楚. 而这里很不清楚. 

##### 2) d-separataion 在概率上的意义.  

这个地方非常重要:

![](./pictures/19)

这个定理分了两步去讲, 第一步讲:

- 在 X 和 Y 被 Z 在 G 中有向分离的话, 那么, X 在 Z 已知的条件下, 关于 Y 独立. 
- 在 X 和 Y 被 Z 在 G 中没有被有向分离的话, 那么, X 在 Z 已知的条件下, **至少有一个分布**使得其于 Y 有关.  

这里只关注第二条, 这里的至少有一个分布是极为重要的. 因为, 我们可以通过适当的调整G中分布的参数的话, 可以使得, 即使在没有被有向分离的情况 和  X 在 Z 已知的条件下, 关于 Y 独立. 

这也就是为什么使用 **at least one** 的原因了. 

> 可能会有人觉得这一点都不重要, 但是把这里记住是很重要的, 因为在后面的章节中会因为没有理解这一点而浪费很多时间, 比如说我. 

##### 3) 互换定理

![](./pictures/21)

这里需要注意的是第二条, 这里说的是 与G可兼容的所有分布, 这里的潜在意义是, 一个 G 可以对应多个 P, 因为 G 只是描述除了分布的依赖性, 而没有制定其具体的分布形式. 

因此, 可以得到两个重要的推论:

- 一个 P 可以对应着 多个 G
- 一个 G 可以对应着 多个 P

##### 4) 顺序马尔科夫条件

![](./pictures/22)

这里也是很好理解的, ordered Markov Condition 指的是, 在给定某种顺序下, DAG 的一种判断条件独立性的定理 : 在一个点的父节点在给出的情况下, 这个点与其他所有非子孙节点都独立. 

##### 5) 父马尔科夫条件

这个是去除了关于顺序的限制 :

![](./pictures/23)

##### 6) 等价观测

这里给出了一个判断两个 Network 等价的方法. 

- 两个 network 骨架一样 (骨架一样是指, 退化为无向图后是相同的)
- 拥有相同的 'v' 字合流. 

具体的例子见 P20



#### 2.4 Inference with Bayesian Networks

在贝叶斯网络上的推论. 

简单定义来说就是:

对于 根据 X 预测 Y 的模型而言, 其公式为:

![](./pictures/25)

s 就是 贝叶斯网络上除了 X,Y 之外所有变量的集合. 

具体的推论 在 因果推论 中不重要, 这里省略, 有兴趣的可以看 PRML 第八章.







### 3. Casual bayesian networks

#### 3.1 DAG by Casuality

##### 1) 相关关系的不可靠性

在上一节也说到了, 在利用相关关系构建DAG的时候, 其结果是及其依赖于 variable order 的. 

那么,  对于一组变量, 利用相关性去建模的话会得到很多种不同的 DAG 图. 

-> **因此因果的作用就在于能在一开始决定 order, 使得 DAG 只有一个**

##### 2) 因果关系的重要性 

- 不需要性的情况:

  在某些情形下, 人对外界做出的判断甚至是不用经过概率的, 只是利用到了因果关系. (6.1.4)

  对于这种情形, 一般的利用概率图去做的模型是无法做到的. 

- 相关性是因果的副产物

  这个是显而易见的, 正因为有了因果性, 两个变量的概率之间才有了联系, 也就是概率上的相关. 

##### 3) 基于因果的DAG的稳健性

在收到外界的影响时, 基于因果的DAG会受到比较小的影响. 

比如, 前面的喷水器的例子中, 如果我们设定一下雨就关闭喷水器的话. 那么, 我们需要做如下修改:

- 增加 $x_2, x_3$ 之间的连接. 
- 修改 $P(x_3|x_1,x_2)$ 之间的分布形式

若 order 不定的, 那么就需要针对不同的 DAG 设置不同的  $P(x_3|x_1,x_2)$ .



#### 3.2 Causal Networks as Oracles for Interventions

##### 1) Causal DAG 的特点 

###### 1> 模块性

由于 parent-child 之间的稳定以及自治的物理机制. 在不改变其他变量之间分布的独立性的情况下, 单独改变一个 parent-child 之间的分布是**有可能的 ( conceivable ) .** 

###### 2> Informative

一般 DAG : 可以知道一个 event 在之后观测数据中的概率.

因果 DAG : 可以知道在收到外部影响下, 概率的变化. 

所以 : Caual DAG is informative than probilities model.

##### 2) Intervention的例子

###### 1> 对比例子

现在通过一个例子来同时解释 intervention 以及 上面的 Causal DAG 的特点. 

- 喷水器例子的概率图模型:

  ![](./pictures/1)

  ![](./pictures/3)

- 喷水器例子的因果图(with intervention)模型:

  ![](./pictures/2)

  ![](./pictures/4)

###### 2> 分析

- Intervention:

  下面的因果中给出的 intervention 就是 $X_3=on$. 

- 模块性:
  - 在概率模型中, 我们需要根据 $X_3=on$ 作为条件去查询其条件概率,$P(x_1,x_2,x_4,x_5|X_3=on)$. 并且, 由于 $x_1,x_3$ 之间是非独立的, 因此我们会推测出, Season 多是干旱的季节. 
  - 在因果模型中, 我们将其定义为 intervention, $P(x_1,x_2,x_4,x_5|do(X_3=on))$. 即, 将$X_1->X_3$ 去掉.  相比于上面的概率模型, 这里不需要对 $P(x_1)$ 的概率进行改变. 

  这就是因果模型的模块性. 

###### 3> Assumption

上面其实用到了一个假设, 就是 $do(X_3=on)$ 对其父节点没有做出影响. 

即 : 因果图中的节点会根据 autonomy(自主权) 去对 intervention 做出反应. 



##### 3) Causal Bayesian Network 的定义:

$P(v)$ 是一个定义在变量集 V 上的概率分布. 

$P_*= P_x(v)$ 是一个定义在 intervention $do(X = x)$ 上的概率分布, 其中 $X\subseteq V$.

$G$ (Causal Bayesian Network) 是与 $P_*$ 相兼容 (compatible) 的图, 当且仅当下面的三个条件被满足:

1. $P_x(v)$  is Markov relative to $G$;

   > 这个就是指, 因果贝叶斯网络应该符合概率图模型的标准. (不同点是不需要对变量进行 order)

2. $P_x(v_i)=1 \ for\ all\ V_i\in X$   whenever $v_i$ is consistent with  $X=x$

   > 也就是说, 对于被干涉的部分, 其概率 $X=x$ 一定是等于1.

3. $P_x(v_i | pa_i)$ = $P(v_i | pa_i)$ for all $V_i\notin X$ whenever $pa_i$ is consistent with
   $X = x$.

   > 即, 被干涉变量的父节点的概率分布是不变的. 并且注意的一点:
   >
   > **这里讲干涉的分布形式成功转换为了正常的概率分布形式**

##### 4) Causal Bayesian Network 的性质: 

- Truncated factorization

  ![](./pictures/5)

  也就是说, 若一个节点的子节点全是被干涉节点, 我们可以忽略这个节点. 

  并且, 我们只需要考虑被干涉节点的子节点及其非干涉的父节点即可.

- Property 1

  ![](./pictures/6)

- Property 2

  ![](./pictures/7)

  > disjoint就是排反, 即 S 与 $\{V_i,PA_i\}$ 之间没有交集. 

- Property 3

  被干涉变量在 DAG 中变成了 root 节点. 

#### 3.3 Causal Relationships and Their Stability

##### 1) 反向利用因果关系

可以通过对某个变量进行干涉后, 观测其他变量的变化情况以决定 causal effect. 而被干涉变量就是 causal influence. 

具体来说, 如果想要检测 $X_j$ 和 $X_i$ 的关系, 我们需要首先对 $X_i$ 进行干涉. 

那么, 按照上面的理论, 只有 $X_i$ 的子孙才会被这个干涉所影响. 只要看, $X_j$ 的分布前后有无变化即可. 

##### 2) Stability

这里作者给出了一个哲学上的观点:

因果关系是 ontological (存在论) 的, 即真实的因果关系不会根据环境(do-operator)的变化而变化. 即使我们队知识的认识(概率分布)发生了变化. 

概率关系是 epistemic (认识论) 的, 我们对事物的认识(概率分布)会随着环境的变化(do-operator)而变化. 

这个解释太棒了. 



### 4. Functional causal model

#### 4.0 哲学宗旨

##### 1) History

在历史上引用因果模型用的多是决定性的函数性公式, 他们都是基于**拉普拉斯的假设, 即因果性是确定性的**, 而之所以实际数据中出现这么多随机性是由于我们对于潜在的边界的模糊性造成的. 

然而, 因果贝叶斯网络是与此完全相反的. **其本身就存在随机性.** 

##### 2) 本书宗旨

但是接下来, **我们使用的因果模型是基于 拉普拉斯的确定性假设的. **

这一点非常重要, 直接影响了后面使用的概率模型. 

下面介绍理由:

1. the Laplacian conception is more general. 

   一个随机模型可以被很多很多的概率函数所模拟. 这个存在不合理性, 解释见下面. 而拉普拉斯的确定性假设不存在这个问题. 

   > 有条件的话可以看下, Bayesian statistics lee 4th 的 2.10.4 (虽然我也只看这一节)
   >
   > 这节提到 : Is your prior really conjugate?
   >
   > 答案是否, 正如我们在PRML笔记第二章中所提到的那样, 之所以可以设定共轭先验后验的形式就是因为, 每个分布族都有足够大的取值可能性去模拟合适的分布. 那么, 真实分布中共轭先验以及似然函数匹配只是为了计算方便. 
   >
   > 但是,另一方面, 每个分布其实都有自己的物理含义. 例如卡方分布代表:
   >
   > $Y=\sum_iX_i^2$ . 	其中, $X_i$ 代表遵循正态分布的随机变量. 这个就是卡方分布所包含的先验知识. 
   >
   > 那么, 很明显这里存在着不合理性. 因为, 由于先验后验的设定使得关于分布的先验知识被强加在问题上了. 

2. The Laplacian conception is more **in tune with hu­man intuition**.

   我们要搞清楚, 确实根据量子力学的观点, 一切都是随机的. 确实根据量子力学实验表明, 这种随机性是本质存在的, 因此物理学家必须要摒弃 deeply entrenched intuitions - locality and causality.

   但是, 我们这里要去保护他. 因为, 这里要做的不是客观地描述物理现象, 而是描述人对物理现象的认识. 即因果论的中心是人. 

3. Certain concepts that **are ubiquitous in human discourse** can be defined only
   in the Laplacian framework .



#### 4.1 Structural Equations

##### 1) 结构方程

###### 1> 定义:

![](./pictures/8)

$x_i$ 是目标变量. $pa_i$ 是 $x_i$ 的因s.  $u_i$ 表示误差.

其代表着一种 代表 au­tonomous mechanism. 

###### 2> 例子:

- 例子, 线性结构方程模型(SEMs), 被用于经济学以及社会科学等等:

  ![](./pictures/9)

##### 2) 其他概念

- Structural model : 结构方程的集合叫做Structural model
- Dependent variable : 一个离散的变量
- Structural causal model : 如果每个 mechanism 只决定一个离散的变量, 那么这个模型就叫做  Structural causal model, 或者 Causal model

##### 3) 与代数方程的区别

Mathematically, the distinction between structural and algebraic equations is that the latter are characterized by the set of solutions to the entire system of equations, whereas the former are characterized by the solutions of each individual equation. 

即, 对于一个式子来说, 这个是没有区别的. 

但是对于多个式子组成的集合 来说, 是不同的. 

- 代数方程组 : 
  - 其整体作为一个个体存在, 其中的一个方程和其他方程之间存在着限制的关系. 这种限制关系是瞬时的. 
  - 缺少了一个方程, 这个方程组就不再描述的是一个问题. 
- 结构模型(Structural model) : 
  - 其每个结构方程本身就是作为一个个体存在, 其本身就有合理性, 方程与方程之间存在的是一种随着时间变化的动态限制关系.
  - 缺少了一个方程, 模型依然合理, 因为每个方程本身都具备合理性

**这种个体性, 其实就是上面一直说的 自治性 (autonomy)**

##### 4) 左右变量的不一致性

注意到上面的定义中, 有Dependent variable这个概念. 但是这个概念其实只针对每个式子中的左侧的变量. 也就是说, 左侧和右侧的变量的实质是不同的, 左为 "果", 右为 "因". 

区别可以通过介入这个action去体现, 一个在右侧的变量永远都是作为一个 "因" 存在, 而我们影响的只能是 "果". 



#### 4.2 例子

##### 1) 例子说明

下面是一个供需模型的因果图:

![](./pictures/10)

wages : 是制造物品所需要的本金.

$U_1, U_2$ 是误差. 

其构造方程式如下:

![](./pictures/11)

##### 2) intervention 分析

本小节目标在于理解 autonomy 这个词在因果模型中的意义:

- 这两个方程构成了一个环状图, 代表了一种动态稳定结构. 而 autonomy 是指, 两个公式由于自己的 machanism 构成的一个缓慢的互相牵制的模型. 

- 介入:

  现在, 假设 $$\operatorname{do}(p=p_0)$$ , 例如政府对物品的价格进行了限定. 

  可以发现, 对于 Q 而已, 其因果方程依然是不变的. 

  这种面对外部干涉, 不受干涉的方程依然保持不变的性质称为 **关于动态变化的自治性** . 



#### 4.3 三个基本问题

- **预测 ( predictions ) : **would the pavement be slippery if we find the sprinkler off?
- **干涉 ( intervention ) : ** would the pavement be slippery if we make sure that the sprinkler is off?); 
- **反事实 (counterfactuals)** : would the pavement be slippery had the sprinkler been off, given that the pavement is in fact not slippery and the sprinkler is on? 

本书也就是围绕这三个基本问题展开的.



#### 4.4 因果模型中的概率预测

##### 1) 基本概念

- **Causal diagram** (**因果图**)

  对于结构方程

  ![](./pictures/8)

  我们将结构模型 (structural Model) 中的每个公式都用点和有向边去表达的话, 就会得到一个有向图, 这个就叫做 **causal diagram**

- **Semi-Markovian (半马尔科夫模型) **

  如果 causal diagram 是无环的, 那么就称其为 **Semi-Markovian**

  由于误差因子 $U$ 的存在, 所以每个变量还是具有随机性, 我们将这种随机性用概率分布去表示. 因此, $P(x_1,x_2,...,x_n)$ 是由 $P(U)$ 决定的. 

  > 这里之所以说是被 $P(U)$ 其决定的. 是因为, 其他决定性的因子都可以被当做线段来表示. 

- **Markovian(马尔科夫模型)**

  如果假设每个 $u_i$ 之间是独立的, 即 $u_i$ 都是 root 节点的话, $U$ 中变量之间相互独立. 

  再加上非循环性, 可以说不同点的误差是 互相独立的. 

  > 如果是有循环的话, 则不可能每个 $u_i$ 之间是独立的. 

**下面就以 马尔科夫模型 为目标进行研究**



##### 2) Causal Markov Condition (因果马尔科夫条件)

![](./pictures/12)

即, 在祖先节点被给出的情况下,  $X_i$ 条件独立于所有非子孙节点. 



##### 3) 为什么使用马尔科夫假设

为什么我们假设因果模型都符合马尔科夫假设呢?

- Every variable that is a cause of two or more other variables.

  要把含有两个以上子节点的点放入模型中, 因为这样的点存在与不存在对其子节点之间的独立性有影响, 即:

  ![](./pictures/13)

  这个情况下:![](./pictures/14)

  ​

- 基于common-cause assumption.

  即, 因果中两个变量有关联的点必然是以下两种情况之一:

  - 两者存在因果关系
  - 两者有共同的因. 也就是父节点. 

  很明显, 在我们的限制于无环的因果模型 (Structural Model)里面, 两者都不存在.

因为我们使用的是拉普拉斯的假设, 因此每个变量里的随机成分是独立的. 也就是每个 $u_i$ 都是root点. 

因此也就解释为了为什么马尔科夫模型在因果中这么常用. 



##### 4) 确定性假设的意义

没有使用 $P(x_i|pa_i)$ 作为其分布的好处. 

区别在于, 在确定性假设中, 每个变量 $x_i$ 都能连接到一个 root 节点, 也就是 $u_i$. 

- Imposes equivalent independence constraints on the resulting distribution
- leads to the same recursive decomposition that characterizes Bayesian networks

并且这两个性质和 结构函数 (Structural function) $f_i$ 的选择以及 误差的概率分布 $P(u_i)$ 的选择都没有关系. 



##### 5) 预测问题(三个基本问题之一)上的分析

functional specification 要比 probabilistic specification 模型好. 原因如下:

1. 用functional specification去表示的因果图的条件独立性质是不会随着参数的变化而变化的. 

   > 首先要明白这里的参数是什么意思 : 就是上面讲到的, 结构方程式中的系数等等. 这里的参数在一个因果模型中是固定的, 但是其本身可以变化从而构成不同的结构模型.
   >
   > 但是, 为什么说这里是不会变的呢? 4.0 节也说到了, functional specification 的哲学设计理念就是: 我们对于两个event之间的因果关系是确定的, 也就是说, 它不会出现参数为 0 的情况, 参数为0的话, 就应该将那个线撤掉.  
   >
   > 而基于 概率分布形式的因果模型, 或者说是概率模型, 其event之间的条件独立性质是不直观的, 因此可能会因为参数的变化而变化. 

2. 参数少, 便于计算.



#### 4.5 Interventions and Causal Effects in Functional Models

这一节考虑 intervention 给 functional model 带来的影响, 以及变化策略. 

##### 1) Example

这个在前面的例子中其实已经提到, 在将喷水器强行打开后:

- 其与父节点的连接要断开
- 还要将其 与子节点的分布改为条件分布. 

##### 2) General form

当全部的变量集 $X$ 中的 一部分 $x$ 被 intervent 之后, 变化是:

- Functional Model 中的 一部分 Functional equation 会被移除. 
- 按照 3.2.4 的公式, 对 distribution 进行修改. 

##### 3) Functional Model关于表现 intervention 的优点

具有灵活以及通用性

- Intervention 的分析可以扩展到 cyclic Model, 即在不修改任何规则的情况下. 而其他的不行, 因为因果模型本身具有自律性. 
- 模型可以更好地理解, 没有了贝叶斯的复杂推导, 整个模型变得简洁易懂. 并且, 在求条件分布的时候, 可以简单的使用求导去处理, 而不用进行复杂的贝叶斯推导.
- 使用Function Model 使得在非马尔科夫的模型中的推导变得简单. 
- Functional Model 允许分析特定于上下文的动作和策略. 理解不了这里. 



##### 4.6 Counterfactuals in Functional Models 

##### 1) 问题点

传统统计模型无法解决反事实问题, 因为没有在某一事件没有发生时, 另一事件的数据 ....

这一节看不懂, 到了第七章再详细解释吧. 



### 5. 因果术语及统计术语

由于这两者本质虽是不同的概念, 但是存在大量的相似的地方. 因此, 非常需要对其进行区分. 这节非常重要. 

#### 5.1 Parameter

- **probabilistic parameter**  : 

  在联合分布中被定义的任意的量. 可以是分布的参数, 也可以是一些统计量(期望, 方差,本质也是参数. )

- **statistical parameter** : 

  无论有没有未观测变量, 仅仅定义在已观测变量上的联合分布的参数. 比如:

  ![](./pictures/26)

- **causal parameter :**

  ![](./pictures/27)

  定义在一个因果模型上的参数, 这里的因果模型就是上面提到的那些 基于结构方程的模型. 那么参数就是, 里面的系数, 或是参数的运算方式. 还包括图的相关信息以及加入intervention 之后的效果. 

#### 5.2 Assumption

- **statistical assumption:** 

  这个是限制在已观测变量的联合分布上的假设, 当然也可以对具体的边缘分布进行假设.

- **causal assumption :**

  使用了 causal model 模型的假设, 例如方程式的结构, 以及未观测变量的数目及相关性. 

  - 而  causal assumption 是可以包含 statistical assumption 假设的. 例如, 已观测变量之间的独立关系. 这些假设都是可以被验证的. 
  - 但是, 不属于 statistical assumption 的 causal assumption 不是全部都可以被验证的. 



#### 5.3 因果模型和统计模型的区别

判断区别有两个重要的点:

- 变量之间有无时间顺序(也可以是其他顺序). 因为, 在后面出现的变量是无法成为前面的变量的 casual 的. 
- 有无causal assumption, 比如有无对未观测变量的假设. 不管有无未观测变量, 不能不对其加以假设, 这个假设属于causal assumption. 



#### 5.4 因果与统计的概念

统计概念:

- statistical parameters
- correlation
- regression
- conditional independence
- association
- likelihood
- collapsibility
- risk ratio
- odds ratio
- 等等

因果概念:

- causal parameter
- random­ization
- influence
- effect
- confounding
- exogeneity
- ignorability
- disturbance
- spurious correlation
- path coefficients
- instrumental variables
- intervention
- explanation
- 等等