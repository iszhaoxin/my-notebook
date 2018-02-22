

## Imbalance data



### 1. 问题提出

#### 1) Imbalance problem 

在分类问题中很有可能出现数据不平衡的问题. 

比如, 正样本和负样本的比例非常小, 例如我的实验中, 可取的负样本的数目是正样本数目的几千倍, 这个时候会出现什么问题呢?

- 全部采用的方式:

  会使得模型失去能力, 只会向偏向多的标签的方向预测.

- 采用部分采样的方式

  会存在大量未训练的标本, 使得在test集上具有很弱的正确率.

根据博客中的描述, 现实世界中的很多分类问题是不平衡的, 例如提出投诉的人总比不投诉的人少很多. 

而样本比例如果大于 4:1 的时候, 就会出现问题. 

#### 2) Accuracy Paradox

准确性悖论指的是, 在样本严重不平衡的情况下, 出现的高预测结果其实只是反应了类别分布的结果而已. 也就是说 accuracy 虽然高, 但是 recall 和 precision 很低的问题. 

> 这里值得注意的一个问题是, accuracy 虽然和 precision 和 recall 一样, 都只是评价实验结果的一个标准, 但是实际上 accuracy 还是具有很不一样的性质的. 那就是, accuracy 是和loss直接挂钩的, 而 loss 是进行学习的根本. 

### 2. 方法

#### 1) Collect more data

请问这个算不算废话, 能收集到为什么要用 imbalance data ...

#### 2) Try Changing Your Performance Metric

利用其他的指标去评价效果, 这个只能帮助分析, 并不能解决实际问题.

#### 3) Try Resampling Your Dataset

- 对少量的样本进行重采样.
- 对多的样本进行under-sampling, 即删掉一部分.

这个只用于一般的不平衡数据, 但是我的任务是上千倍的差距, 所以不行.

#### 4) Try Generate Synthetic Samples

这个不同于过采样(over-fitting), 这个方法采用的是生成新的 minor class 样本的方法来增加少量样本类的样本数. 采取的方法叫做 : Synthetic Minority Over-sampling Technique (SMOTE).

通常的方法是根据距离函数, 或者插值等方法合成新的样本. 

以下为资源:

- [SMOTE: Synthetic Minority Over-sampling Technique](http://www.jair.org/papers/paper953.html)
- sklearn的imbalance_data资源, “[UnbalancedDataset](https://github.com/fmfn/UnbalancedDataset)”

#### 5) Try Different Algorithms

找到更适合处理这种问题的方法.

#### 6) Try Penalized Models

通过改变loss配比的方法去惩罚模型, 也就是说:

对于少量类的样本的loss, 乘以一个大的倍数, 这样即使只有少量的样本也能使得其有大量的loss.

#### 7) Try a Different Perspective



#### 8) Try Getting Creative

发挥想象力, 比如, 将大的类别分成小类等等.