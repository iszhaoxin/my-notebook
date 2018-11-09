## Poincaré Embeddings for Learning Hierarchical Representations



### 0. Abstract 

- 首先这篇文章的 motivation 是捕捉 latent hierarchical 信息. 

- 使用方法是 : embedding them into hyperbolic space - 更精确的说是一个 n-Dim Poincare ball. 

- 为什么这个方法可以呢? 

  因为这个结构的自带的 双曲几何(罗巴切夫斯基几何)性质, 可以使其同时捕捉到 Hierarchy 和 similarity 信息. 

- 本文有接着提出了一个基于黎曼优化 embedding 学习方法. 



### 1. Introduction

#### 1.1 线性空间的局限性

线性的 embedding 方法被证明对维度具有较大的依赖, 也就是说, 在线性 embedding 法中, 对于表现复杂的 pattern , 需要更高的维度才可以. 

根据先行研究, 知道以下几个结论

- linear embedding 需要较大的维度 :

  > Maximilian Nickel, Xueyan Jiang, and Volker Tresp. Reducing the rank in relational factorization models by including observable patterns.  NIPS 2014. 

- Non-linear embedding 可以解决这个问题. 

  > Guillaume Bouchard, Sameer Singh, and Theo Trouillon. On approximate reasoning capabilities of low-rank vector spaces. AAAI 2015. 

#### 1.2 现实数据的复杂性

尽管有以上的各种方法, 但是没有哪个方法可以保证有一个方法可以在没有任何信息损失的情况下, 在低维上对数据进行编码. 

#### 1.3 利用数据中的层级性

作者本文的目标是找到一个高效的表示方法, 那么这个方法就必须可以包含 Hierarchy 信息. 

现实数据中的层级性可以从 power-low distribution 等等的现象中得知, 其是存在的. 下面有一个经验性的研究表明, 在信息网络中存在 tree-like 的结构. 

> Aaron B Adcock, Blair D Sullivan, and Michael W Mahoney. Tree-like structure in large social and information networks. ICDM 2013

#### 1.4 Hyperbolic space

本文通过将 knowledge 编码在 hyperbolic 空间, 来提高效率. 

Hyperbolic space : space with constant negative curvature. 