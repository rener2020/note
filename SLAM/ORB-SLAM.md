---
title: ORB-SLAM
category: 2022-09-01
renderNumberedHeading: true
grammar_cjkRuby: true
---


# 单目初始化
- 入口
![](./images/1662027724145.png)
- 未初始化则初始化
	- 初始化帧
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
	- 初始化器，设置参考帧
	 ![enter description here](./images/1662035242010.png)
	- 构建orb特征旋转直方图
	  ![enter description here](./images/1662102631734.png)
	 - 初始化相机位姿与图像特征三维点
	  ![enter description here](./images/1662102665966.png)
	- 地图初始化
	  ![enter description here](./images/1662107437198.png)
	  
	  
# 初始化之后进行跟踪
- 

