---
title: ORB-SLAM
category: 2022-09-01
renderNumberedHeading: true
grammar_cjkRuby: true
---


# 单目初始化
- 入口
![![enter description here](./images/1662027631371.png)](./images/1662027724145.png)
- 未初始化则初始化
	- 初始化帧
	![](./images/1662027621081.png)
	- 跟踪入口
	![enter description here](./images/1662027759223.png)
		- 提取特征
			![enter description here](./images/1662035482796.png)
		- 去畸变
			![enter description here](./images/1662035516072.png)
		- 分配特征加速计算
		  ![enter description here](./images/1662036380614.png)
	- 单目初始化
	  ![enter description here](./images/1662027824693.png)
	- 初始化器
	  ![enter description here](./images/1662035242010.png)
- 初始化之后进行跟踪
	- 

