## Improving zero-shot learning by mitigating the hubness problem



### 1. Introduction

#### 1.1 Zero-shot learning

这个问题是说, 我们有两个空间, 一个是object的特征空间, 一个用word表示的 label space. 

We can use limited training data avaliable to learn a general mapping function to map vectors in the domain of interest to word vectors. 



使用源数据被投射点所处的拓扑结构去纠正最近邻检索 