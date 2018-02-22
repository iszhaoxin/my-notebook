

训练强关系的方法.

以语言模型为例子

- 找到序列$(x_t,x_{t-1},...,x_1)$中一个词汇的上层词汇 $(x_t^h,x_{t-1}^h,...,x_1^h)$

  那么应该有 $x_i \mathcal{\leq} x_i^t$

- 假设, $f(x_t,x_{t-1},...,x_1) = x_t$

   $f(x_t^h,x_{t-1}^h,...,x_1^h) = x_t^h$

- 那么, 就应该有:

  $x_t \mathcal{\leq} x_t^h$



这个可以用强化学习去学习出来.

学得的模型可以是基于nerual graph network的.