## SOCKEYE:A Toolkit for Neural Machine Translation

### 0. abstract

这是一个可再生产的序列到序列的神经翻译框架,该框架了提供了三种训练和推断的框架:attentional recurrent neural networks, self-attentional transformers, and fully convolutional networks.该框架还提供了一系列关于优化,归一化和正则化的工具,使得训练过程可自由定制和加入新的想法.



### 1. Introduction

这一段真难读,英语太捉急怎么办.

首先,论文说到了,目前的NMT的进展很快,没过几个月就会有一个state of the art算法出现.一方面确实提高了模型的质量,但是另一方面,对模型的使用造成了难度,因为太多不一样的模型.莫

在实现者要去使用的时候,经常被要求达到在翻译质量以及计算性能上的双最佳(production-ready).但是现在的NMT模型取得的进展都是在一次次微小的模型调整,超参数调整以及基于经验的有效启发式算法下实现的.与SMT不同,NMT没有一个拥有所有最好主意的,标准的平台,可以吸引整个领域的注意力.现在NMT领域中各个独立的模型使得对比架构和算法的提升度变得困难.

为了解决这个问题,提出了这个SOCKEYE框架,集合现在的最主流的三个框架:

-   注意型循环神经翻译模型(attentional recurrent neural networks):就是我之前最关注的模型
-   self-attentional transformers:一个完全基于attention的翻译架构,就是之前谷歌的那篇文章
-    fully convolutional networks:完全使用CNN来构成Seq2Seq模型,facebook论文,超越谷歌

SOCKEYE提供了三种模型并允许用户自行添加代码.

下面来简要介绍三种模型.



### 2. Encoder-Decoder Models for NMT

序列到序列的模型就是说,输入一个序列输出一个序列的模型.输出的根据是,输入的向量以及网络内部的状态信息.包含一个Encoder和一个Decoder.下面来步骤化公式化介绍这个概念模型的基本框架.

-   训练集:

    输入:$X=x_1,...,x_n$		输出:$Y=y_1,...,y_m$

-   NMT system models(模型的目标):

    $p(Y|X)$->$p(y_t|X,y_{1:t-1}|\theta)$

-   其中 $x_i,y_t$均为one-hot向量,即:

    $x_i∈ \{0,1\}^{|Vsrc|}$

    ​	$y_t∈ \{0,1\}^{|Vtrg|}

    其中,$Vsrc$为原语言词汇库,$Vtrg$为目标语言词汇库.)

-   每个词汇还拥有一个词嵌入向量,用$E_Sx_i$表示$x_i$的词嵌入向量,用$E_Tx_t$表示$y_t$的词嵌入向量,其中

    $E_S \in \mathbb{R}^{e \times |V_src|}$		 $E_T \in \mathbb{R}^{e \times |V_trg|}$		

-   最后的公式:

    $p(y_t|Y_{1:t−1},X;θ) = softmax(W_o\bar{s_t}+b_o)$

    其中$\bar{s_t}$是网路的输出向量,$W_o$是将$\bar{s_t}$放到目标词汇集$V_{trg}$的维度,去利用softmax函数计算每个词汇的概率.

在这个最一般的encoder-decoder模型中,我们使用RNN去encoder一个输入,但是,当输入序列稍微长一些的时候,定长的输出序列向量也许不能存储足够的序列信息,这时注意力机制可以通过一个权重来选择性的注重序列的不同部分,以达到好的效果.

随着高度并行设备(GPU)的使用,并行的encoder/decoder计算变得非常重要.

下面来介绍这三个模型:



#### 2.1 Stacked RNN with Attention

这个之前看过,就不仔细介绍了,看之前的笔记



#### 2.2 Self-attentional Transformer

具体细节见all you need is attention的笔记.