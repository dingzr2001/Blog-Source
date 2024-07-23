---
title: AdaDelta and Adam Algorithm
tags: [machine learning, optimization]
categories: [machine learning, Dive into Deep Learning-Notes, optimization]
date: [2023-02-18 17:56:00]
index_img: /img/adadelta.jpg
---

# AdaDelta and Adam Algorithm

## AdaDelta

AdaDelta is another variant of AdaGrad. Like RMSProp, it solves the problem of relying too much on previous gradients, by leaky average,  but in a more complicated way. Here is how it works.

First, like RMSProp, we have:
$$
S_t=\rho S_{t-1} + (1-\rho)g_t^2
$$
but unlike RMSProp, we don't upgrade W directly with S. We have a iterative equation:
$$
\begin{cases}
M_t = \rho M_{t-1}+(1-\rho)G_{t-1}^2\\
G_t = \frac{\sqrt{M_{t-1}+\epsilon}}{\sqrt{S_t+\epsilon}}\cdot g_t
\end{cases}
$$
If we combine them into one equation, we have:
$$
G_t=\frac{\sqrt{\rho M_{t-2}+(1-\rho)G_{t-1}^2+\epsilon}}{\sqrt{S_t+\epsilon}}\cdot\nabla W_t
$$
And we update W with G:
$$
W_t = W_{t-1} - G_t
$$
The iterative equation part could be quite confusing. The sequence of calculation and update should be:

1. gradient($g_t=\nabla W_t$)
2. $S_t$
3. $G_t$
4. $M_t$
5. $W_t$

In AdaDelta, we have no learning rate $\eta$, that means we don't need to set a hyper-parameter by ourselves.



## Adam

We use a algorithm to combine RMSProp and momentum method. 
$$
m_t = \beta_1 m_{t-1} + (1-\beta_1)g_t
$$

$$
v_t = \beta_2 v_{t-1}+(1-\beta_2)g_t^2
$$

It is suggested that we set $\beta_1$ to 0.9 and $\beta_2$ to 0.999. To correct the discrepancy between the expectation of v and $g_t^2$, we need to correct the v:
$$
\hat{v_t}=\frac{v_t}{1-\beta_2^t}
$$
Likewise, we could also correct m by:
$$
\hat{m_t}=\frac{m_t}{1-\beta_1^t}
$$
And now we could update W by:
$$
W_t=W_{t-1}-\eta\frac{\hat{m_t}}{\sqrt{\hat{v_t}}+\epsilon}
$$
About the correction of discrepancy part, the author gives a inference in the original paper:https://arxiv.org/pdf/1412.6980v9.pdf?

### Yogi

We can rewrite the formula in Adam to:
$$
v_t=v_{t-1}+(1-\beta_2)(g_t^2-v_{t-1})
$$
If the gradient is too big, Adam algorithm may fail to converge. To fix this problem, we have Yogi algorithm:
$$
v_t=v_{t-1}+(1-\beta_2)g_t^2\cdot sgn(g_t^2-v_{t-1})
$$


## References

https://arxiv.org/pdf/1412.6980v9.pdf?

https://zh-v2.d2l.ai/d2l-zh-pytorch.pdf

https://blog.csdn.net/weixin_35344136/article/details/113041592

https://blog.csdn.net/ustbbsy/article/details/106930309

