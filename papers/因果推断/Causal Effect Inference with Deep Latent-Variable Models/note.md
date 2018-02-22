## Causal Effect Inference with Deep Latent-Variable Models



### 1. Introduction

#### 1.1 Causal Effect Inference

Intervention(干预) 是因果推论中一个极为重要的概念, 即 $P(L|do(D))$ . 对于干预的理解, 见我的笔记 [因果推论]:Introduction. 

一个很经典的例子是, 分析吃某种药(D), 能否延长寿命(L). 只从观测数据中只能得到$P(L|D)$. 但是会有很多其他因素影响使得 $P(L|D)\neq P(L|do(D))$. 这是因为, 也许会有其他因素影响着 $P(L|D)$. 比如, 买得起D的人家里都很有钱, 也就可以做很多其他的治疗等等. 

一般来说, Intervention是需要根据需求设计实验来进行分析的, 但是, 在真实数据中是很难做到这一点的, 因此, 发展出了很多只基于 observation data 就可以得到 Intervention 数据的分析方法.  这些分析方法统称为 Causal Effect Inference.

#### 1.2 Confounding

Confounding 是很重要的一种**推断因果关系**的方法.

Confounding 的假设是, 有一个变量既可以影响到 intervention 因子, 也可以影响到 outcome 因子.  这个变量叫做 *confounder*.  有两种情况

- Confounder 可被计算的情况(即可影响可观测的变量):

  这时, 去分析他的效果的话, 就可以通过控制confounder来实现. 具体的方法有两个:

  - Covariate adjustment
  - Propensity score re-weighting

- Confounder 不可被计算的情况(潜在变量):

  比如说, 社会经济因素是可以影响一个人吃不吃药的, 我们设这个因素为 $Z$, 即潜在影响因子.  但是这个因子是无法计算的. 这个影响因子会影响到我们要做的实验(treatment) $t$. 我们的目标变量 outcome 是 $y$. 

  对于那么对于不可计算的潜在confounder, 我们必须引进新的受 $Z$ 的影响的潜在 confounder $X$. 其不会与 $t,y$ 有直接关系, 但是通过 $Z$ 有间接影响. 他们之间的概率图如下:

  ![](./pictures/1)

#### 1.3 Proxy variables

##### 1) What is?

代理变量 : 上面的unobservation过程中, $X$ 其实就是 $Z$ 的代理变量 (proxy variables). 

我们希望用代理变量去模拟真实的不可观测的confounder. 因此, 因果模型是需要大数据支持的, 并且需要对数据进行定制, 比如说对于泰坦尼克幸存问题而言, 不是说分析泰坦尼克号沉船时每个人的身份信息就可以了, 还要分析当时的价值观念和社会背景, 但是这些是无法计算的, 因此需要用其他数据去"代理". 比如说, 当时社会主流媒体的新闻报道等等. 

##### 2) How to use?

现在已有一些方法(见下面哭的地方)去处理代理变量, 但是他们都有一个缺点, 他们进行了一个很强的假设. 那就是: 

​		*hidden confounder $Z$ 服从已知类别的分类分布或者线性高斯模型* 

而关于这些方法是什么..........哎, 想哭, 感觉又是另外一个世界了.....下面是讲解这些方法的论文. 

> \- M. Kuroki and J. Pearl.  Measurement bias and effect restoration in causal inference.  Technical report, DTIC Document, 2011.
>
> \- W. Miao, Z. Geng, and E. Tchetgen Tchetgen.   Identifying causal effects with proxy variables of an unmeasured confounder.
>
> \- J. Pearl. On measurement bias in causal inference. arXiv preprint arXiv:1203.3504, 2012.

##### 3) 解除限制

我们无法确定潜在变量服从的是何分布, 这样的话即使有可观测的代理变量 $X$. 我们也很难进行因果推理. 这里作者提出了一个方法解决这个问题.

**这个方法的特性是** : 可以在拥有大量代理变量的时候的推理, 大量代理变量叫做 surrogate-rich setting.

**这个方法的想法是** : 对一种**潜变量模型**进行估计, 在估计的同时可以发掘潜在的confounder $Z$ , 并对 $Z$ 如何影响和实验(treatment)和效果(outcome)进行推理. 具体的, 我们瞩目于**基于最大似然的近似推断**. 下面是原文:

> Estimation of a latent-variable model where we simultaneously discover the hidden confounders and infer how they affect treatment and outcome. Specifically, we focus on (approximate) maximum-likelihood basedmethods.

##### 4) 潜变量模型

- 什么是

  潜变量模型是指模型中不仅仅有可观测数据还有一些不存在于数据中的变量, 例如变分自编码器, 比如基于随机优化方法求解变分下界的模型(recognition networks).

- VAE

  这里使用的模型是VAE. 原因是, VAE对数据生成过程使用了很弱的假设, 也就是用了很弱的限制.  对隐藏confounder $Z$ 的结构也没有什么限制. 更加自由.



### 3/2 Supplementary 

为了理解下面一节的内容, 需要补充以下知识:

##### 1) Causal quantities(因果量)

因果量是和causal model(causal diagram)相关的量, 而和定义在因果图上的变量的分布 $P_M(v)$ 无关. 那么这个量是什么呢?



### 2. Identification of causal effect

