## Learning in Implicit Generative Models



### 1. Introduction

#### 1.1 一般生成模型及其缺点

- Implicit probabilistic models 

  定义一个随机过程可以随机生成数据.

- Implicit generative models

  生成模型生成数据的过程分为两步:

  - 从一个随机过程中生成随机变量: $z' \thicksim q(z)$
  - 利用一个决定性的映射方程生成数据 : $x=\mathcal{G}_{\theta}(z')$

- 生成数据的概率密度分布

  ![](./pictures/1)

  这个函数说白了是一个 **质量分布的微分** -- **密度分布**

  - 最右边对所有使 $\{\mathcal{G}_{\theta}(z)\leq x\}$ 的z的概率进行积分, 这样和 $z$ 有关的分布就成为了和 $x$ 有关的分布.
  - 和 $x$ 有关的分布是一个概率质量分布, 下一步通过微分变为关于 $x$ 的概率密度分布.

  > 这个函数存在的意义是, 我们要计算生成数据的分布, 但是无法直接得到, 因为他是一个隐含分布的映射得来的, 我们无法知道这个映射是否是单调的, 因此我们要用: $\{\mathcal{G}_{\theta}(z)\leq x\}$ 来确保这一点. 

- 计算概率密度分布的缺点

  - 无法计算 $\{\mathcal{G}_{\theta}(z)\leq x\}$
  - 高维求导很难实现

#### 1.2 解决方法

两个解决模型 : GAN和ABC

两个模型都是一个区分生成数据和真实数据中进行学习的一个过程.

这篇文章的目的是揭露其中的概率原理 -- 无概率推理的核心原理. 并且讨论两者的异同.



#### 1.3 明确问题和notation

目的 : 找到数据的真实分布 $ p^∗(x)$

我们模型模拟出来的数据分布 : $q_{\theta}(x)$, 

隐含变量z的概率密度分布 : $q(z)$

利用隐含变量 $z'$ 去生成 $x$ 的模型是 : $\mathcal{G}_{\theta}$, 其参数为 $\theta$

对真实数据和生成数据进行辨别的辨别器为 : $\phi$



### 2. Hypothesis Testing and Density Ratios

#### 2.1 Likelihood-free Inference

##### 2.1.1 没有概率分布怎么活

在目前为止所有的模型中, 无法希望做的就是找到一个function去模拟数据行为, 无论是监督还是非监督. 这个function其实就是我们对数据建立的**概率分布模型**. 但是除此之外还有些什么方法呢?

- Method-of-moments 
- Empirical likelihood
- Monte Carlo sampling(即用采样去代替期望计算-来自强化学习)
- Mean-shift estimation

##### 2.1.2 Estimation-by-Comparison

###### 1) 核心

来自对于生成数据分布和真实数据分布的比较. 接着用比较的信息去作为学习改进 implicit generative models 的基本原则.

###### 2) 操作方法 

Density estimation-by-comparison

具体方法就是:

- **Comparison**

  先将生成数据分布和真实数据分布进行比较, 得到两者的差距信息

  - **Density difference** : $r(x) = p^∗(x) − q(x)$
  - **Density ratio** :  $r(x) =p^∗(x) / q(x)$

- **Estimation:**

  利用这个差距信息去改进我们的生成数据模型.

###### 3) **几种Comparison的方法**

这是几种不同的推理思路, 但是最后都可以推导至统一的结果.

- class-probability estimation
- divergence minimisation
- ratio matching
- moment matching

###### 4) **整个框架**

这里需要注意的是, 损失函数是关于模型参数和辨别函数的.

![](./pictures/2)



#### 2.2. Class Probability Estimation

alternating optimisation : 轮流优化

surrogate negative log-likelihood :  代理负对数似然 

##### 2.2.1 Density ratio

![](./pictures/3)

这里就是简单的应用了个贝叶斯定理而已.

其中的 $\pi$ 不是圆周率, 而是对于正例(真实数据)和负例(生成数据)的比例的设定, 一般为0.5, 为均衡的情况, 也有不均衡的情况.

看到这个式子里面有一个新的函数出来 : $p(y|x)$, 这个就是辨别器.

##### 2.2.2 Loss方程 

- **辨别器 (discriminator)**

  ![](./pictures/5)

  就是判断输入数据为真实数据的可能性. 后面会用到一个 $\mathcal{D}(\mathcal{G}(z;\theta))$ 的式子, 即用来判断生成模型生成的数据被认为是真实的可能性, 当然我们希望最小化这个概率就可以提高辨别器的性能.

- **辨别率和Ratio的关系**

  $R=D/(1-D)$

- **Loss函数**

  我们最后的目标是可以得到一个以真乱假的分布模型, 也就是最小化$R$, 因此, 需要对上面的式子进行最小化, 得出的负对数似然损失函数如下:

  ![](./pictures/4)

##### 2.2.3 Bilevel optimisation

双层优化. 其要解决的问题是, Stackelberg competition

> Stackelberg competition: 
>
> 问题描述:有一个leader, 有一个follower. follower的行为会根据leader的行为而变, 这个时候要求leader去考虑全局, 要求做出一些动作可以使两者的行动造成的结果可以达到最优.
>
> 解决方案:这个时候leader就不仅仅要考虑自己的举动, 而要预测自己的举动给follower带来的影响, 以及两者的行动对于全局效果的影响. 这里的解决方法是, 采用双目标函数进行优化. 这种情况下, 我们称:
>
> Upper-level objective function : 对应的是leader's problem
>
> Lower-level objective function :  对应的是follower's problem.

在这个设定中, 我们认为:

Upper-level objective function : 要解决的是分布建模问题, 即要最小化 $\mathcal{L}$ : **Ratio loss**

Lower-level objective function :  $\mathcal{L}$ 要用到辨别器的模型, 因此辨别器是lower问题, 需要最小化预测失败的概率 : **Generative loss**

- **Ratio loss** : 

![](./pictures/6)

- **Generative loss**

  ![](./pictures/7)

这也是GAN的主要思想.

##### 2.2.4 其他Loss函数

![](./pictures/8)

Bernoulli loss 是有很多缺点的. 具体的区别在一篇论文中有写:

**Strictly proper scoring rules, prediction, and estimation.**

##### 2.2.5 其他问题

###### 1) 优化问题

由于loss函数是非凸函数, 因此无法保证收敛. 

我们最后要做的是使得真实分布和生成分布的距离最近. 类似于求两个分部之间的 KL距离.

在GAN中, 使用Bernoulli loss函数的情况下, 其分布的距离计算类似于 **Jensen Shanno disvergence(JS距离)**, 而不是KL距离.

这里先记下一个问题 : **Wasserstein distance**, 一个很重要的分布距离计算公式, 有时间看.

###### 2) 2.2.6 $\pi$ 的问题

上面说到 $\pi$ 是用来反应正例和负例的数据量的平衡程度的.

但是最近的研究显示, $\pi$ 的设定应该根据问题而定, 它是与**JS距离**的计算有关的.具体看相关论文



#### 2.3 DIvergence Minimisation

这个是直接从最小化分布距离来计算的. 具体数学太弱看不懂



#### 2.4 Ratio matching

使用最小二乘法直接最小化真实的 ratio 与ratio的近似来实现.

依然不知道它在说什么.....



#### 2.5 Monent Matching

判断真实分布与生成分布的矩是否相等.

好吧...





