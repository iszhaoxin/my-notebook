## 9. 混合模型和EM



### 9.0 EM算法

在本章开始之前, 有必要先清楚 EM 算法是什么. 

#### 9.0.1 Backgroud

##### 1) MLP和MAP

这两个都是最大化数据发生概率去学习预测模型的方法. 

MLP : $\theta = \operatorname{argmax}_{\theta}\prod_i P(x_i|\theta)$

MAP : $\theta = \operatorname{argmax}_{\theta}\prod_i P(x_i|\theta)P(\theta)$

这两者的区别是, MAP使用了先验分布, 提升了模型的健壮性, 属于 生成式模型.

##### 2) MLP和MAP的不足

###### 无解析解情况的存在

这两个算法都是对数据模拟进行解析式的计算. 包括先验概率和后验概率的对应, 参数的更新等等. 具体的笔记见 [贝叶斯统计] (如果我有时间写的话)

###### 计算量过大

对于某些存在潜变量的情况, 即 $P(x_i|\theta)$ 是一个关于 $\theta$ 不可微分的函数. 比如, $\theta$ 是离散的情况下的 GMM. 那么, 对于一个点与其目标值的pair的发生概率而言, 我们的目标是求其联合分布, 即:
$$
P(X) = \sum_{z}P(X,Z)
$$
对于每一个点 $x$ 都需要对c个类别进行判断, 因此,  对于所有的点, 其结果就是:
$$
\sum_{\operatorname{s}}=\sum_{z_1}\sum_{z_2} \cdot \cdot\cdot\sum_{z_i}\cdot \cdot\cdot\sum_{z_n}
$$
每个点都有c个可能性:

![](./pictures/1)

对于n个点, 就有 $c^n$ 个可能性. 无法计算.

#### 9.0.2 Log-sum -> Sum-log

潜变量连续的状况就是 log-int -> int-log 

##### 1) 监督学习和非监督学习的区别

- 监督学习:

  由于这个情况下, 输入和输出已经给出, 因此不需要考虑 类条件概率 $P(x|t)$ 与 先验概率 $P(t)$ 的依赖性情况. 因此:
  $$
  \arg\max P(X) \to \arg\max\log P(X) \\\log P(X)=\log P(X|Z) + \log P(Z)
  $$

- 非监督学习:

  由于一开始对于数据和类别都是不确定的, 因此两者是不可独立的. 
  $$
  \arg\max P(X) \to \arg\max\log P(X) \\\log P(X)=\log\sum_Z P(X,Z)
  $$
  这里就出现了 log-sum. 这是EM算法首先要处理的问题.  因此, EM 算法针对的问题都是非监督性问题.

##### 2) log-sum 不可用

log-sum 微分后的结果是一个有理式, 即分子和分母均为多项式的分数.  计算起来相当复杂. 或者说是不可推导. 再加上上面提到的极大的组合性的问题. 因此不可计算

##### 3) Sum-log 为什么可以

sum-log的 微分形式是简单的 sum 的形式, 处理起来很简单. 因此我们需要将 log-sum 的问题, 转化为 sum-log 的问题.



#### 9.0.3 EM算法精要

##### 1) 目标函数的分解

我们的目标函数, 按照第一章第五小节的定义是最小化损失期望:
$$
\operatorname{decrete} : J(\theta)=-\log P(X|\theta)=-\log \sum_x\sum_zP(x,f(x|\theta)) = -\log\sum_zP(X,f(x|\theta))\\
\operatorname{continous} : J(\theta)=-\log P(X|\theta)=-\log \int_x\int_zP(x,f(x|\theta)) = -\log\int_zP(X,f(x)|
\theta)
$$
这里, 设 $\int_xP(x,f(x|\theta)=i)dx=g_i(\theta)$, 则:
$$
\operatorname{decrete} : J(\theta)=-\log P(X|\theta)=-\log \sum_z g_z(\theta)\\
\operatorname{continous} : J(\theta)=-\log P(X|\theta)=-\log \int_z g_z(\theta)\\
$$


> 这里之所以用 sum 的原因是, x之间是独立同分布的关系. 
>
> 为了方便表示, 下面使用 $\sum$ 表示

而我们的目标是最大化这个函数即:
$$
\theta = \arg \max_{\theta} J(\theta)
$$

##### 2) EM算法

通过某种转换(下一小节), 我们证明 log-sum 大于等于 sum-log 的形式, 因此, sum-log 作为 log-sum 的下界 (lower bound) 的形式存在. 

1. Exceptation : 找出, 现在模型下数据的概率的 (lower bound) 值, 即sum-log的值. 
2. Maximum : 通过最大化 lower bound 的方式去更新参数. 使得真实概率 $\log P(X)$ 也提升.



#### 9.0.4 lower bound - Discrete

##### 1) 离散情况

离散的情况下, 可以参考 GMM 模型

##### 2) Jensen's inequality 

对于任意的凸函数 $f(x)$ 而言:
$$
\forall \lambda_i\geq 0,\:x_i\:(i=1,\cdots,n),\:\displaystyle\sum_{i=1}^n\lambda_i=1
$$

$$
{\displaystyle\sum_{i=1}^{n}\lambda_if(x_i)}\geq f({\displaystyle\sum_{i=1}^{n}\lambda_ix_i})
$$

##### 3) 转换 log-sum 

而log函数本身就是一个凸函数. 并且, 由于是负似然函数, 因此, 这里需要将不等式反过来.

现在需要找出一个 $\lambda$ 和 $f(x)$, 使得:
$$
-\sum_i\lambda_i \log(f_i(x))\leq -\log\sum_i\lambda_if_i(x)
$$

- $\lambda$

  这个要求和为1, 那么一个很自然的想法就是,
  $$
  \lambda_i=\frac{f_i(\theta)}{\sum_if_i(\theta)}
  $$
  也就是, 现在的模型下, 属于第i个类别的点的误差值, 在总体中占得比例.

- $f(x)$
  $$
  f(x)=\frac{f_i(\theta)}{\lambda_i}
  $$


##### 4) 整理一下:

现在, 我们通过转换, 将log-sum, 转换为了 sum-log 的形式:
$$
\lambda_i=\frac{f_i(\theta)}{\sum_if_i(\theta)}\\f(x)=\frac{f_i(\theta)}{\lambda_i}
$$
但是, 有一个问题是, 这里没有分出先后性. 即在没有先后的情况下, 无法体现出 EM 算法的不停更新的特性. 因此, $\lambda$ 和 $f(x)$ 的先后性是不同的. 

$\lambda$ : 其 $\theta$ 是基于之前的状态的, 之前的状态给了一个先验的分布, 设这里的 $\theta=\theta^{old}$

$f(x)$ : 这里是更新的主要来源, 由于我们要做的是最大化 lower bound, 所以 lower bound 中必须要有变量, 这里的变量就是 $\theta^{new}$

因此, $f(x) = g(\theta^{old}, \theta^{new})=f_i(\theta)/\lambda_i(\theta^{old})$

因此, 
$$
J(\theta)\geq\sum_z\lambda_z(\theta^{old})\log \frac{f_i(\theta^{new})}{\lambda_z^(\theta^{old})}=\sum_z\lambda_z(\theta^{old})\log f(\theta^{new})-\sum_z\lambda_z(\theta^{old})\log \lambda_z(\theta^{old})
$$

##### 5) Q函数

在上面的式子中, 前面既有 $\theta^{new}$ 又有 $\theta^{old}$ 的部分就叫做 Q函数. 

log-sum 就成功转换为了 sum-log.



##### 4) 变分推理

$$
\begin{align}
\ln p(X) &= \ln p(Z,X) - \ln p(Z|X) \\&= \ln \frac{p(Z,X)}{q(Z)} - \ln \frac{p(Z|X)}{p(Z)}\\&=\int q(Z)\ln \{\frac{p(Z,X)}{q(Z)}\}dZ-\int q(Z)\ln \{\frac{p(Z|X)}{q(Z)}\}dZ
\end{align}
$$



### 9.1 K均值聚类

#### 9.1.1 K均值聚类的思想

##### 1) 条件

有一堆在D维的数据 x. 还有要分类的类别数 K

##### 2) 目的

找到K个类别的中心点, $\mu_k$.

使得每个点到其最近的类别中心店距离的平方和最小. 即:
$$
J=\sum_{n=1}^N\sum_{r=1}^K||x_n-\mu_r||^2
$$

##### 3) 方法

EM算法是一种方法的统称, 其有最抽象最基本的公式, 但是投射到不同问题中去就会有不同的结果. 

