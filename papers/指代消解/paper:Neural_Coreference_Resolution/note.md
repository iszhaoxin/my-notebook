## End-to-end Neural Coreference Resolution



### 1. Task

这个模型是第一个 end2end 的 neural 模型用于指代消解. 

本研究目的只有一个就是指代消解, 但是要做的事有两个:

- 找出entities, 每个Entity可能是几个词汇的组合(也就是span)
- 找出entities之间的指代消解关系

#### 1.1 Notation

##### 1) 输入部分

- $D$ : 文档的符号
- $T=|D|$
- $N=T(T+1)/2$ : 可能的 span 数
- $i$ : D 中的第 i 个span. $START(i),END(i)$ 分别是开始位置和结束位置. 

##### 2) 输出部分

- $y_i$ : 包含了 span i 前面的span (**antecedents**) 与它的关系, 其取值范围为 $\mathcal{Y}(i)=\{\epsilon,1,...,i-1\}$, $$y_i=j$$, 就代表了这第 i个 span 和第 j 个span 互为指代关系
- $\epsilon$ : 可能有两个意思. 
  1. span i 不是entity
  2. span i 是entity, 但是span i 在前面没有与其指代的entity.



### 2. Model

#### 2.1 目标

目标是学得对每一个 span 的 $y_i$ 的分布. 也就是: $P(y_1,...,y_N|D)$

而我们的数据集中, 对每一个 $D_i$, 都有一个对应的 $y_1^i,...,y_N^i$. 

#### 2.2 细节

##### 1) 关于目标函数

其中, 不同的 $y_i$ 是分开计算的, 即相互独立的:

![](./pictures/1)

> 这里可以改进

其中, $s(i,y_i)$ 是一个pairwise score用来指示, span i 和span j (即$y_i$) 之间是否有指代关系. 

我们要做的就是找打可以最大化数据集中 $P(y_1,...,y_N|D)$ 的参数. 

##### 2) 关于 $s(i,y_i)$

![](./pictures/2)

其中, $s_m(i)$ 表示 span i 是一个entity的分数. 

$s_a(i,j)$ 表示两者具有指代关系的分数.

两者的计算公式为:

![](./pictures/3)

$g_i$ 是第 i 个span的 Span representation. 

FFNN是一个简单的end to end的神经网络.

对于每一部分标记的详细解释见下面的章节

##### 3) Span representations

1. 先利用LSTM学得 word representation $x_t$.

   ![](./pictures/4)

2. 利用 attention 机制, 组合 word representation 到 span representation. 其中 $\alpha_t$ 是word t 的权重. $\alpha_{i,t}$ 就是在 span i 中 word t 的权重. 

   ![](./pictures/5)

   ​

   注意, 这里很奇怪的地方是两点:

   1) 计算 $\alpha$ 居然没有用到句子信息

   2) 计算  $\alpha$ 的网络居然和计算 $s_a(i,j )$ 的方法一模一样. 

3. 组合成 span representation

   ![](./pictures/6)

   其中 $\phi(i)$ 是 span 的长度. 

   这里的组合方式可以说是相当怪异了, 居然这么强调start词汇和end词汇, 但是好像还是有理有据, 来自论文:

   > Kenton Lee, Shimi Salant, Tom Kwiatkowski, Ankur Parikh, Dipanjan Das, and Jonathan Berant. 2016.Learning recurrent span representations for extractive question answering

对于利用span representation进行y的score过程如下:

![](./pictures/10)



#### 2.3 Span pruning strategies

如果不对span的数目进行限制, 这个算法的复杂度将是 $O(T^4)​$

因此这里采用了一些策略:

##### 1. 最大长度

span 的最大长度为 $L$

##### 2. 按照mention分数删选候选span

留下前面的 $\lambda T$ .mention分数就是 $s_m$ 

##### 3. 最远 antecedents

也就是 $y_i$ 考虑的最远范围, 这里设为 K.

##### 4. 无交叉

考虑两个 span 的关系时, 这两个span一定不能有共同的词汇, 反映在 start 和 end 上就是不能出现如下情况:

$START(i) < START(j) ≤END(i) < END(j) ∨ START (j) < START(i) ≤END(j) < END(i).$

##### 5. Gold clustering

我们称满足上面条件的 span 属于 **gold mention.**

所有的 gold mention 都属于 Gold clustering

当一个 span 不属于 Gold clustering 的时候, 他将不纳入计算考虑. 反映在loss函数上就是2.4节中的 $\operatorname{GOLD}(i)$

#### 2.4 Learning

![](./pictures/7)

这个就是最后的学习目标.

如果 i 不属于 gold clustering, 或者 其前面的 antecedents 全部被剪掉了的时候, $\operatorname{GOLD}(i)=\{\epsilon\}$

一个问题 :  需要注意的是, 由于这里有 span pruning, 并且pruning strategies中有一个和位置无关的 mention 分数 $s_m$ 作为标准, 这就给一开始的随机初始化带来了问题, 这样的话只会有一部分span会被训练, 这个会有什么影响吗?

答 : 不会, 因为标注数据是固定的, 所以无所谓初始化什么.

#### 2.5 整体框架

![](./pictures/9)



### 3. Experiments

本文记载了详细的参数. 其最后结果还是很好的:

![](./pictures/8)



