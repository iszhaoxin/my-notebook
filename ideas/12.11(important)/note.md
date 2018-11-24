## 极为重要



两个 embedding space 的映射, 可以有很多种类型:

- image 到 word : caption
- word to word : translation
- word to entity : entity linking
- entity to entity : knowledge alignment



每个模型使用的mapping方法是不同的,  这里的mapping 方法的选择取决于转换的难度. 

例如, 从 image to word 使用的是joint的非线性神经网络. 

而, 从word to word使用的是pipline 的 线性变换. 从这个里面就可以看出一些有意思的事情. 

首先, 我们猜想, joint的能力>pipline. 其次, non-linear neural > linear transformation

这样说明, word to word 的变换是个很简单的形式. 

那么, 我们是否可以学得一个general的框架去自动学得这个变换呢?

我的想法是, 提供一个对抗学习的框架, 辨别器对目标任务进行运算, 返回loss.

生成器生成的是 模型的参数以及对于模型参数的限制(包括连续性, 等等泛函分析中的知识)

这里最重要的就是对模型参数的限制, 这个限制可以限制住模型的能力. 

> 当然这个过程中的negative sampling是非常重要的. 

问题在于, 如何去限制模型的能力? 这个过程是离散的还是连续的? 连续的话是什么样? 非连续的话是什么样?



