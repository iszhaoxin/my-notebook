
### 谱聚类



#### 1 拉普拉斯矩阵

**定义**

拉普拉斯矩阵是针对图结构的.

$L=A-D$, 其中 

A 是一个对角线矩阵, $A_{ii}$ 是点 i 的度数. 

D 是一个邻接矩阵, $D_{ij}$ 储存的是点i和点j之间的权重.

**性质**

1. 对称矩阵, 且特征值都是非负.

2. 对于任意 维度为 $|E|$ 的**初始向量 $f$** 而言 

   $f^TLf = \frac{1}{2}\sum\limits_{i,j=1}^{n}w_{ij}(f_i-f_j)^2$, 具体推到过程为:

   $f^TLf = f^TDf - f^TWf = \sum\limits_{i=1}^{n}d_if_i^2 - \sum\limits_{i,j=1}^{n}w_{ij}f_if_j$

   由于 D 是度数的对角线矩阵, 因此 $d_i=\sum_j^{|E|} w_{ij}$

   $f^TLf =\frac{1}{2}( \sum\limits_{i=1}^{n}d_if_i^2 - 2 \sum\limits_{i,j=1}^{n}w_{ij}f_if_j + \sum\limits_{j=1}^{n}d_jf_j^2) \\=\frac{1}{2}( \sum\limits_{i,j=1}^{n}w_{ij}f_i^2 - 2 \sum\limits_{i,j=1}^{n}w_{ij}f_if_j + \sum\limits_{i,j=1}^{n}w_{ij}f_j^2) \\= \frac{1}{2}\sum\limits_{i,j=1}^{n}w_{ij}(f_i-f_j)^2$

   **这个性质, 现在还看不到用处, 不过一会就用到了**

#### 2 谱聚类的目标函数

-  **谱聚类的目的** : 

  对一个已知图进行**切分**, 注意是**切分**, 而不是求node向量.

- **Insight** : 

  切后的子图内的连接数应该尽可能大, 而子图之间的连接数应该尽可能小.  

- **设计目标函数** : 

  根据这一点设计图聚类的目标函数, 首先设置两个子图之间的连接数的函数 : 设A,B是两个子图  $W(A, B) = \sum\limits_{i \in A, j \in B}w_{ij}$

  RatioCut切图 : $RatioCut(A_1,A_2,...A_k) = \frac{1}{2}\sum\limits_{i=1}^{k}\frac{W(A_i, \overline{A}_i )}{|A_i|}$

  Ncut切图 : $NCut(A_1,A_2,...A_k) = \frac{1}{2}\sum\limits_{i=1}^{k}\frac{W(A_i, \overline{A}_i )}{vol(A_i)}$

- **目标函数的分析** : 

  可以看到, 只是分母不一样, 第一个分母说的是子图$A_i$中点的个数, 第二个分母说的是子图$A_i$中所有内在连接数. 当然第二个更符合我们的要求, 按照我们的要求, 需要

  **「子图内的连接数应该尽可能大, 而子图之间的连接数应该尽可能小.  」**

  也就是说, 分子应该尽可能小, 分母应该尽可能大, 那么我们就要最小化目标函数.

#### 3 谱聚类与拉普拉斯矩阵

直到现在其实都没有发现拉普拉斯矩阵和谱聚类的关系. 关系是什么呢?

因为我们无法直接对谱聚类的目标函数进行优化, 因此需要某种方法去进行转换问题, 使得谱聚类的目标函数转换为一个我们可以去简单求解的方法. 经过科学家的伟大探索, 发现了拉普拉斯矩阵. 也就是说:

**不是先发明了拉普拉斯矩阵, 而是根据需要创造出了拉普拉斯矩阵**

#### 4 谱聚类与拉普拉斯转换

这里以第二个目标函数为例.

- **注意点** : 

  再次强调, 上面拉普拉斯矩阵使用的**初始向量**并不是我们要求的node向量, 而是一个 $|E|$ 维的向量, 可想而知其应该储存的是与图切分有关的信息.

- **初始点向量的设计** : 

  我们的目标是进行图切分, 首先我们假设切分好的子图为k个 $(A_1,A_2,...,A_k)$.

  其次, 设 $h_j$ 中储存的数据是, 与 $f$ 代表的点处于同一个子图 $A_j$ 的index处标志为 $\frac{1}{\sqrt{|A_j|}}$  , 因此 $h_j$ 是针对子图的向量. 这个解释不容易明白, 看公式:

  设 $h_j$ 为 $h_j =\{h_{j1}, h_{j2},..h_{jn}\}^T\; \ j =1,2,...k,n=|E|$

  $h_{ji}= \begin{cases} 0& { v_i \notin A_j}\\ \frac{1}{\sqrt{vol(A_j)}}& { v_i \in A_j} \end{cases}$

  因此, $h_j$ 组成的矩阵 $H=[h_1,h_2,...,h_k], H\in \mathbb{R}^{n*k}$ 中的各行向量正交. 这里的 $H$ 也就是我们要求解的对象.

- **拉普拉斯矩阵和谱聚类的关系**

  $\begin{align} h_i^TLh_i & = \frac{1}{2}\sum\limits_{m=1}\sum\limits_{n=1}w_{mn}(h_{im}-h_{in})^2 \\& =\frac{1}{2}(\sum\limits_{m \in A_i, n \notin A_i}w_{mn}(\frac{1}{\sqrt{vol(A_j)}} - 0)^2 +  \sum\limits_{m \notin A_i, n \in A_i}w_{mn}(0 - \frac{1}{\sqrt{vol(A_j)}} )^2\\& = \frac{1}{2}(\sum\limits_{m \in A_i, n \notin A_i}w_{mn}\frac{1}{vol(A_j)} +  \sum\limits_{m \notin A_i, n \in A_i}w_{mn}\frac{1}{vol(A_j)}\\& = \frac{1}{2}(cut(A_i, \overline{A}_i) \frac{1}{vol(A_j)} + cut(\overline{A}_i, A_i) \frac{1}{vol(A_j)}) \\& =  \frac{cut(A_i, \overline{A}_i)}{vol(A_j)} \\& = NCut(A_i, \overline{A}_i) \end{align}$

#### 5 谱聚类目标函数的优化

- **最终的优化目标**

  上面得出的是对某一个子图进行二切分后得出的目标函数, 那么对k个子图进行切分的话就有:

  $NCut(A_1,A_2,...A_k) = \sum\limits_{i=1}^{k}h_i^TLh_i = \sum\limits_{i=1}^{k}(H^TLH)_{ii} = tr(H^TLH)$

  这里就出来了一个很容易计算的目标: $H^TLH$ 的迹.

  $\underbrace{arg\;min}_H\; tr(H^TLH) \;\; s.t.\;H^TDH=I$

  其中, $H^TDH=I$ 是因为:

  $h_i^TDh_i = \sum\limits_{j=1}^{n}h_{ij}^2d_j =\frac{1}{vol(A_i)}\sum\limits_{v_j \in A_i}w_{v_j} = \frac{1}{vol(A_i)}vol(A_i) =1$

- **近似算法+node向量的生成**

  上面这个还是一个NP难问题, 需要测试 $k2^n$ 次.

  近似方法是采用特征值分解的思想, 我们知道 L 是实对称矩阵, 因此其特征向量两两正交.同时上面也说道, $H$ 中各向量正交.

  那么, 我们是否可以忽略 $H$, 使用L的特征向量去推测 $H$? 可以的.

  由于我们只要K个 $h$, 且 $k<n$, 那么我们就去特征值最小的k个特征向量即可.

  但是这里的特征向量并不是单位向量, 因此需要将其转换为单位向量,即:

  $h_{ij}^{*}= \frac{h_{ij}}{(\sum\limits_{t=1}^kh_{it}^{2})^{1/2}}$

  并且, 不同于之前的稀疏向量, 这里的新的 $H'\in \mathcal{R}^{n*k}$ 是一个密集矩阵, 因此, 可以用这个做**node的向量**.

  接下来, 再根据node向量进行 K-means 聚类即可.