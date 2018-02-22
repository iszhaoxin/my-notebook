## MLE vs MAP



#### 1. 共同点

- both a method for **estimating some variable** in the **setting of probability** distributions or **graphical models**. 

- 都是**点估计**, 而不是对一个分布的估计, 也就是说, 需要得估计的都是点, 而不是分布. 

  > 即使求的是分布的参数, 最后结果也都是求一个而已.



#### 2. 不同点

- 形式不一样

  - MLE是针对一般的形式来说的, 即:
    $$
    % <![CDATA[
    \begin{align}

    \theta_{MLE} &= \mathop{\rm arg\,max}\limits_{\theta} P(X \vert \theta) \\[10pt]
                 &= \mathop{\rm arg\,max}\limits_{\theta} \prod_i P(x_i \vert \theta)

    \end{align} %]]>
    $$

  - MAP是针对**贝叶斯的有潜在变量的形式**说的, 即:

    不仅在似然函数中有所求的 $\theta$, 在先验函数中也有 $\theta$.
    $$
    % <![CDATA[
    \begin{align}

    \theta_{MAP} &= \mathop{\rm arg\,max}\limits_{\theta} P(X \vert \theta) P(\theta) \\[10pt]
                 &= \mathop{\rm arg\,max}\limits_{\theta} \log P(X \vert \theta) P(\theta) \\[10pt]
                 &= \mathop{\rm arg\,max}\limits_{\theta} \log \prod_i P(x_i \vert \theta) P(\theta) \\[10pt]
                 &= \mathop{\rm arg\,max}\limits_{\theta} \sum_i \log P(x_i \vert \theta) P(\theta)

    \end{align} %]]>
    $$


总言之, 唯一不同就是, MAP中有先验 $p(\theta)$

