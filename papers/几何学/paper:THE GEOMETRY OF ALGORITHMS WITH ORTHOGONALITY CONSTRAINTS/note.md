## Introduction - The geometry of algorithm with orthogonality constraints

#### 1.1 Background

- 这篇文章处理的是拥有约束性的优化问题, 例如:
  - 线性以及二项式约束 : linear and quadratic constraints
  - 正交性约束 : The Stiefel and Grassmann manifolds 上的优化具有正交约束.
- 这篇文章提出了一个可以处理类似约束问题的framework
- 需要的背景知识有 : 数值线性代数, 最优化理论, 微分几何



#### 1.2 Stiefel和Grassmann流形

##### 1) 特点

- 两者都是有 Orthogonality constraints（正交约束）的流形 (与 symmetric eigenvalue problem 相同)



#### 1.3 Grassmann manifold

- **优化对象:** $F(Y)$

- **优化对象的限制:**

  - 正交性假设

    $Y^TY=I,Y\in \mathbb{R}^{n*p}, n>p$, 说明其是正交矩阵. 

  - 同质性假设:

    $F (Y ) = F (Y Q), Q\in \mathbb{R}^{p*p}$, 并且要求这里的Q是正交矩阵. 

    > 也就是说, 在这个系统中, 矩阵 Y 经过一个正交变换 Q 后, 在 F 的映射下的结果是不变的.

- ​