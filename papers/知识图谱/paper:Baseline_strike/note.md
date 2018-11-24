## Knowledge Base Completion : Baselines Strike Back



### 1. Introduction

每当一个任务有了大量的研究之后, 都会有这么一篇警钟论文出现. 

这里要处理的任务是 Knowledge graph embedding. 

这篇论文的观点是 : 我不是针对谁, 我是说在座的各位Model都是XX. 

总而言之, 论文用微调后的 DisMult, 超过了绝大部分的先行研究的算法. 并提出了自己对于这个方向下一步应该发展的方向. 



### 2. Method

#### 2.1 复现

论文将模型分为了四种进行复现:

- 不附带信息的模型
- 带 Path 信息的模型
- 带 Text 信息的模型
- 自己的模型: 
  - 基于 DisMult
  - 从 negative samples number , bathsize,  下降学习算法 几个角度对远模型进行调整.

#### 2.2 结果

![](./pictures/1)



#### 3. Conclusion

#### 3.1 关于超参数的设置

- batchsize 对于结果的影响很大:

  ![](./pictures/2)

- 现在的 evaluation metrics 不合理:

  对于 hit@10, 大部分模型都已经得到不错的结果, 像 hit@1, 和 MRR 都是还不错的.

  但是, hit@1 有一个问题是, 很多 triplets 中, 对于 tail entity 的预测, 其结果本身就可能不是一个, 比如 (? , IsA, Fruit) 中, 可以是水果的 entity 有很多. 

#### 3.2 对结果的分析

- 绝大部分没有信息的模型反而会更好
- 在不同的数据集上, 不同模型会有不同的效果. 
- Ensemble DisMult 可以 outperform 绝大部分的先行模型. 

#### 3.3 对机器学习评价本身的疑问

进一步证实了很多研究的最优结果不过是调参的结果. 

