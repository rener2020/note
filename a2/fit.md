---
title: fit
category: 2023-02-11
renderNumberedHeading: true
grammar_cjkRuby: true
---


$$
z\begin{bmatrix}
u\\v\\1\\\frac{1}{z}
\end{bmatrix}=KT\begin{bmatrix}
x\\y\\z\\1
\end{bmatrix}
$$

在实验环境中，已知：
1. 相机2的位置
2. 特征点在相机1、2中的坐标

求解：
相机1的坐标

思路：
通过特征点在相机1、2中的坐标信息作为误差，调整相机2的位置，直到特征点坐标误差为0

