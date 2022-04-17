---
title: SURF
category: 2022-04-17
renderNumberedHeading: true
grammar_cjkRuby: true
---


加速鲁棒特征(Speed Up Robust Feature, SURF)和SIFT特征类似，同样是一个用于检测、描述、匹配图像局部特征点的特征描述子。
![enter description here](./images/1650179648130.png)

# 积分图
积分图是一种用于快速计算矩阵图像区域像素之和的数据结构，积分图中(x,y)处的值是原始灰度图像左上角与当前点所围成的矩形区域内所有像素点灰度值之和。
![enter description here](./images/1650180091196.png)
![enter description here](./images/1650180098427.png)

# 近似DoH金字塔
