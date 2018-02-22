## Imbalance data



### 1. Introduction

#### 1.1 适用问题

Imbalance data的问题常常出现在二分类的问题中, 不过不同于一般的二分类任务, 这里的分类的目的是把少数的类别从多数的类别中分离出去.

#### 1.2 基本方法

##### 1) Re-sampling

- Undersampling : 通过减少一些多数样本, 使得两种样本的量得到平衡.
- Synthetic minorities : 通过合成新的少数样本来使其平衡.
- Cost-senstitive variants : 给少数样本更多的cost值.

#### 1.3 本文目的与结果

通过严谨的理论的分析, 分析这几种方法. 指出在不同任务中, 哪个方法会更有效.

通过分析, 得知 Undersampling 是简单而有效的方法. 

虽说 Undersampling 有效, 但是其问题在于, 他是一个高方差的方法.  undersampling 一开始采样获取的样本对之后的结果影响很大. 

为了解决这个问题, 可以采取 bagging variance-reduction ensemble method 方法. 也就是 bagging的方法.

#### 1.4 本文贡献

1. 对于分析定量化不平衡数据的影响发展了一套理论. 分析显示, 结果模型会偏向于少数样本, 也就解释了在test 数据集上, recall值退化的原因. 
2. 在此基础上, 我们将这里的 bias (也就是偏向的原因) 分解为了一个子组成部分,  其中的一些影响了训练sample的属性. 另外的一些调整了经验损失函数的计算. 
3. 基于这个分解, 我们分析了几种处理不平衡数据的方法, 以及其可以work的不同的情况.  
4. 从理论和实验的角度, 解释了bagging可以work的原因. 





### 2.  A theoretical analysis of imbalance

#### 2.1 基本假设

正样本和负样本, 是由两个独立的分布生成的.

#### 2.2 基本的intuition

设少量样本的类别为positive, 设其分布为 $\mathcal{P}$. 

设多量样本的类别为negative, 设其分布为 $\mathcal{G}$. 

由于 $\mathcal{P}$ 的样本量较少, 所以 $\mathcal{P}$ 的分布没有得到很好的学习. 因此可能会包含一些正样本. 因此学得的结果会比较偏向于负样本. 

> 这句话比较理解, 一般来说如果负样本多的话, 结果就会偏向于负样本, 因为这样可以获得较低的loss. 其实这里表达的也是这个意思, 不过是从更严谨的表达. 下面讲为什么会有这样的结果.

<img src="./pictures/1" alt="drawing" width="700"/>

这里, 叉叉代表的是正样本, 也就是少量的样本. 方块代表的是负样本, 也就是多数样本.  假设两者都是由方差相同的高斯分布生成.

那么, 有少量样本的的数量较少, 因此其高斯部分的小概率部分会有较少的样本, 而因为多数样本总数很多, 所以反而本应该属于少数样本的区域反而有不少的多数样本(例外较多的情况). 因此, 区分两者的分割线就会被设立在偏向少数样本一些.



下面对于这个bias进行更详尽的分析,

#### 2.3 The Bias of Empirically Estimated Separators 

##### 1) 任务以及符号描述

这里假设任务是一个在超几何空间中找一个超平面划分两部分类别的任务. 

整个数据集为 $\mathcal{D}$, 可分为正数据集 $\mathcal{D}^+$ 和负数据集 $\mathcal{D}^-$ 两部分. 

其中, 对于给定数据集,满足最优划分的超平面可以有无数个, 我们只需要找到其中的一个. 设这个超平面为 $\mathcal{Rs}$.  设 positive 的空间为 $\mathcal{R}_+$, 设 negative 的空间为 $\mathcal{R}_-$.

假设在 $\mathcal{Rs}$ 下的 negative cost 和 positive cost 分别为 $\mathcal{C}_{fn}$ 和 $\mathcal{C}_{fp}$

##### 2) 一些用于评价的式子

- 评价标准 G-mean

  用于不平衡数据的分类问题的评价标准是 G-mean. 代表的是, 两个类别下的accuracy的乘积的平方根.
  $$
  G = \sqrt{A_p*A_n}
  $$

- 最优超平面

  设最优超平面为 : $w^*$ 

  <img src="./pictures/2" alt="drawing" height="300"/>

- loss相关

  那么loss值就为:

  <img src="./pictures/3" alt="drawing" height="300"/>

  现在假设正样本占总样本的比例为 $\pi$, 设在这种情况下, 我们从 $\mathcal{P}$ 和 $\mathcal{G}$ 下生成的数据集为 $\mathcal{D}_\pi$ . 那么在不同 $\mathcal{D}_\pi$ 下, loss的期望为:

  <img src="./pictures/4" alt="drawing" height="300"/>

  对于某一个具体的 $\mathcal{D}_\pi$ 下的 loss 值为:

  <img src="./pictures/5" alt="drawing" height="300"/>

- 实际超平面

  我们假设在某个 $\mathcal{D}_\pi$ 下的超平面为 $\hat{w}$

##### 3) 分析最优与实际的超平面

按照我们上面图像的分析, 分割平面会偏向于正样本, 即:

<img src="./pictures/6" alt="drawing" height="300"/>

下面是对这里的定义:

<img src="./pictures/7" alt="drawing" width="600"/>

也就是说, 最优平面的下的loss值是最小的. 这里的平面大小的定义是根据loss值来决定的.

##### 4) 问题转换

什么时候 $w^{\lambda}$ 会存在 ? 也就是什么时候会出现不准确的(偏置的) 超平面? 

答案是, 在实际最优超平面 $w^*$ 下, 会出现一下公式的情况:

<img src="./pictures/8" alt="drawing" height="300"/>

左边指的是, 实际为负但是被标注为正的cost值, 也就是下面这个图中, $\hat{w}$ 和 $w^*$ 之间的这一部分出现的正例造成的cost. 右边指的是 这一部分间负例造成的cost.

<img src="./pictures/1" alt="drawing" width="500"/>



#### 2.4 Why Weighted Empirical Cost Minimization is not Sufficient

##### 1) 理论推导 

<img src="./pictures/8" alt="drawing" height="300"/>

上面这个式子中, 共有三个要素, **cost 权重** , **正负比例** 和 **分布的特征**.

其中, 改变cost权重以分配给少数样本更多权重是一个很常用的方法. 

但是, 改变cost函数的部分结构是无法解决问题的. 即:

如果构成class中每个instance之间是相互独立的话, 那么修改cost的权重比例是起不到根本作用的. 但是是有效的. 

> 我的理解如下, 首先解释两个地方: **起不到根本作用** 和 **有效的**
>
> 若样本分布均匀或者说采样都足够多, 也就是说在每个区间都有一定量的样本的话. 那么这个方法是适用的. 但是如果在该有正样本点的某个区间内是没有正样本点的, 那么无论增大多少错误的正样本的loss值, 超平面也不会改变了.  也就是说, 改变cost比例是有效的, 但是当样本空间不够大时, 就会失效. 

##### 2) 定量分析

那么, 我们的样本该多到什么时候才会有效呢?

首先我们来看, 上面的那种该存在却不存在的点, 实际在数据集中出现(即存在)的概率为:

<img src="./pictures/9" alt="drawing" height="300"/>

-  $\pi$ 上升

  这的 $\pi$ 是少数派的比例.

  那么, 当 $\pi$ 上升的时候, 可以看到概率增加, 这种概率上升, 也就是说, 优化效果会更明显. 而对于 $\pi$ 较小的情况, 就会发现没什么太大用处.

- $|\mathcal{D}|$ 上升

  当数据集上升时, 就会发现概率也上升了, 也就越有用.

- $\mathcal{P}$ 更紧密

  $\mathcal{P}$ 越紧密, 超平面其周围数据越多, 效果越好

#### 2.5 Remarks on SMOTE

SMOTE就是指合成少数样本的方法.

这个本质和上面的方法是一样的, 因为不会扩展出新的边界. 



### 3. Undersampling and bagging

讲为何这两者有效

#### 3.1 Why Does Undersampling Work?

Under sampling 就是简单的舍弃一部分多数样本, 使得负样本和正样本数相同. 那么对于上面的那个不等式而言, 就会有如下变化:

<img src="./pictures/8" alt="drawing" height="300"/>

<img src="./pictures/10" alt="drawing" height="300"/>

这样的话, bias 就会小很多.  

其实这个的直观理解也很简单. 减少多数样本使得多数样本的分布也没有得到充分学习, 这样就使得两个分布的效果进行了一个持平, 最后也就得到一个比较合理的结果.  效果图如下, 这里采用了十个undersampling下的结果, 相比于之前的结果要很多:

<img src="./pictures/11" alt="drawing" width="600"/>



#### 3.2 Bagging for Imbalance

Bagging 和 undersampling 方法是非常合拍的. Undersampling 最大的问题就是在于高方差的问题. 就例如上面的图像中, 学得了很多的结果, 对于一个新的instance, 通过选择其中拥有较多投票的结果, 可以得到较为准确的预测率. 

#### 3.3 总结

Undersampling 本身就拥有较好的效果, 再加上 bagging , 爽歪歪.



### 4. 实验

实验就不赘述了,  大概就是个利用y作为条件, 再利用随机分布生成x的方式, 来生成数据集, 其概率图如下:

<img src="./pictures/13" alt="drawing" width="500"/>

看最后结果.

##### 1) 随着维度的变化

<img src="./pictures/12" alt="drawing" width="800"/>

这里的横轴是输入数据的维度, 竖轴是效果. 从左到右分别是 $\pi=0.05, \pi=0.1, \pi=0.2$ 的情况.

对于 2.3 中的 empirical-cost 策略来说, 空间太大是不好的, 因为这样增加了空白区的概率. 正如所料, 在 (a), (b) 的图中, 当维度增加时, 效果变差. 但是当 $\pi=0.2$ 的时候, 反而会有提升. 那是因为, 这个时候的比例已经足够去解决问题了.

##### 2) 随着 trainset size的变化

<img src="./pictures/14" alt="drawing" width="800"/>

这个里面, 可以看到trainset越大, 几者之间的距离是越来越小的. 对于 empirical-cost 策略来说, 在 trainset 后期变大时, 其值不高的原因是, 这个时候, 多数样本已经足够多了, 但是少数样本还是很相对而言很少. 

##### 3) 效果比较

Bag+undersampling > undersampling > weighted > smote > baseline

