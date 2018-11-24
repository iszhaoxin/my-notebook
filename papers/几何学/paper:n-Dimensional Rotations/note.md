## General n-Dimensional Rotation



### 1. Background

#### 1.1 The n-Dimensional Translation

这里论文打算用一个 n 维旋转的最一般方法去表示 2维和三维的旋转方法. 

完成一个旋转过程从条件到结果有四个变量:

- 输入 : $\mathbf{x}=(x_1,x_2,...,x_n)$
- 输出 : $\mathbf{x'}=(x_1',x_2',...,x_n')$
- 距离矩阵 : $\mathbf{d}=(d_1,d_2,...,d_n)$
- 旋转函数(作用于d) : $T(\mathbf{d})$



#### 1.2 The arctan2 function

一般的, 对于 $\tan(\theta)=m$ 的情况, 可以知道 $\theta=\arctan(m)$

对应的, 若知道 $\tan(\theta)=y/x$, 可以定义 $\arctan2(y,x)$, 细节为:

![](./pictures/1)



### 2. Introduction



