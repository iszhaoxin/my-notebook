## Hierarchical Density Order Embeddings



### 0. Abstract

本文利用分布而不是点向量来表示词向量, 这种方法可以获取词汇的不确定性以及丰富的可解释性的语义信息. 引入不确定性后可以引入一些蕴涵关系 - 更加广泛的词汇例如"entity"可以包含一些更具体范围的词汇"animals"等等. 这里提出了 density order embeddings , which learn hierarchical representations through encapsulation of probability distributions. . 

具体而言, 提出了一个新的loss function和一个新的 distance metric, 还有一个graph-based negative sampling 方法. 

使用该训练方法在 wordnet 的上位词预测和HYPERLEX lexical entailment数据集上取得很好的结果.



### 1. 简单总结

- 什么是order embedding
  - 介绍了 高斯embedding.
  - 介绍了高斯embedding在order embedding的应用.
- 介绍了之前的 基于严格部分约束的模型.
- 推出了自己的 基于soft部分约束的模型.
  - loss function
  - distance metric(对称或非对称分布距离函数的区别)
  - graph-based negative sampling
- 在实验处介绍了使用对称和非对称分布距离函数的差距.
  - 对称方法会失去上下方向.
- 训练方法
  - 在wordnet上训练
  - GRADED LEXICAL ENTAILMENT



附加一个介绍分布距离函数的ppt, https://www.slideshare.net/KotaMatsui/a-summary-on-on-choosing-and-bounding-probability-metrics

