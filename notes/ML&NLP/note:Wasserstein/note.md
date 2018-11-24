## Wasserstein metric



### 1. MLE to KL

- 最大似然估计是对已知数据的概率形式进行近似的方法. 例如, 用符合高斯分布的点去近似高斯分布的参数. 

  <img src="./pictures/1" alt="drawing" width="500"/>

- 而其本质是定义在 probability measures 上的 KL 距离, 计算的是 $v_{data}$ 和 $p_{\theta}$ 的距离. 这里的等价关系的证明, 见[博客](https://wiseodd.github.io/techblog/2017/01/26/kl-mle/)

<img src="./pictures/2" alt="drawing" width="400"/>



### 2. KL 的问题

KL-distance 