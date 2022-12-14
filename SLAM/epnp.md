---
title: epnp
category: 2022-12-12
renderNumberedHeading: true
grammar_cjkRuby: true
---
# 3d-2d点
![enter description here](./images/1671002576681.png)

# 加权重投影误差
![enter description here](./images/1670825728757.png)

加权重投影误差计算的是3d点投影到2d点的像素误差的加权平均和，其本质是像素误差。


# 概率
使用exp函数将重投影误差映射到$(0,1)$计算概率
![enter description here](./images/1670825774976.png)


