---
title: AdaGrad and RMSProp Algorithm
tags: [machine learning, optimization]
categories: [machine learning, Dive into Deep Learning-Notes]
date: [2023-02-17 17:53:00]
index_img: /img/adagrad.jpg
---



# AdaGrad and RMSProp Algorithm

### AdaGrad

Standard gradient descend is too scaled to be applied to modern deep neural network. AdaGrad is a effective method to optimize  by adjusting learning rate dynamically and automatically. 

At each step, 
$$
S_t=S_{t-1}+\nabla W_t\cdot \nabla W_t
$$

$$
W_t =W_{t-1}-\frac{\eta}{\sqrt{S_t+\epsilon}}\nabla W_t
$$

The more the weight vector has changed in a dimension,  the more gradient it has accumulated on that dimension.

In AdaGrad, we use $ S_t $ to count the gradient of W in the past, and update W with S. The bigger $ S_i $ is, the smaller the change on $ W_i $ would be. By doing this, we could adjust learning rate on each dimension respectively, according to the frequency and range of changes on each dimension of the weight vector. If your path has gone through a steep slope in the direction of X-axis, you need to slow down in X-axis in case you miss the terminal. But your path is quite flat in Y-axis, so you don't need to slow down too much on that direction.

### RMSProp

The problem of AdaGrad is that it counts on gradients in the past too much, so that it might not respond timely on the sudden change of gradient. That is because S has accumulated too much previous gradient. We want to reduce the impact of the gradient too far away in the past, so here we have RMSProp Algorithm:
$$
S_t=\gamma S_{t-1}+(1-\gamma)\nabla W_t\cdot \nabla W_t
$$

$$
W_t =W_{t-1}-\frac{\eta}{\sqrt{S_t+\epsilon}}\nabla W_t
$$

The only change is the weight $ \gamma $ of $ S_{t-1} $. This makes the portion of previous gradients in current S decay in a growing rate. 

### References

https://zh-v2.d2l.ai/d2l-zh-pytorch.pdf

https://zhuanlan.zhihu.com/p/72039430

https://blog.csdn.net/rpsate/article/details/127320124

https://www.bilibili.com/video/BV1r64y1s7fU?spm_id_from=333.880.my_history.page.click

