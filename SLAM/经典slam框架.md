---
title: 经典slam框架
category: 2022-04-18
renderNumberedHeading: true
grammar_cjkRuby: true
---

![enter description here](./images/1650262060452.png)
# 视觉里程记

# slam问题的数学表述
运动：考虑从$k-1$时刻到$k$时刻，智能体的位置$x$是如何变化的
$$x_k=f(x_{k-1},w_k,u_k)$$
其中$u_k$为运动传感器的数据，$w_k$为噪声
