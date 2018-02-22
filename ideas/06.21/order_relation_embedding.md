## order relation embeding

其实是一个很简单的类比order embedding的方法. 
利用的是一个图网络模型. 
其原始输入是两个词汇的order embedding, 输出的也是一个概率分布, 将这个分布视为多个关系之间的混合高斯. 利用EM求解每个模型的比例.



