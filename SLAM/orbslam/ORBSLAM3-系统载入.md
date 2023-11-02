---
title: ORBSLAM3-系统与初始化
tags: 新建,模板,小书匠
category: /小书匠/日记/2023-10
grammar_cjkRuby: true
---


以`/Examples/Monocular-Inertial/mono_inertial_euroc.cc`为例，在`main`函数中，系统现将数据载入之后，初始化SLAM类。

``` cpp?linenums
ORB_SLAM3::System SLAM(argv[1],argv[2],ORB_SLAM3::System::IMU_MONOCULAR, true);
```

在`System`类初始化时，同时初始化`Tracking`，`LocalMapping`和`LoopClosing`线程，并运行`LocalMapping`和`LoopClosing`线程。

之后进行系统追踪
``` cpp?linenums
for (seq = 0; seq<num_seq; seq++)
{
	SLAM.TrackMonocular(im,tframe,vImuMeas);
}
```

在`SLAM.TrackMonocular`中，先存储`IMU`数据，之后通过`mpTracker->GrabImageMonocular`函数计算当前相机位姿。具体而言，程序会以传入的图像**构建图像帧**，在构建图像帧的过程中会提取**ORB特征点**等信息。之后使用`Track`函数进行跟踪。
`Tracking`线程中，根据当前系统状态的执行操作分为以下几个部分
- `NO_IMAGES_YET`：设置零时刻时间，初始化一帧图像帧，将当前状态设置为`NOT_INITIALIZED`
- `NOT_INITIALIZED`：进行**单目初始化**操作`MonocularInitialization`





# 单目初始化
系统中单目初始化一定不会只初始化一次，在第一次初始化时，系统通过`mpInitializer`判定是否是第一次初始化，之后初始化`mpInitializer`并进行一次**IMU预积分**。
在`mpInitializer`初始化之后，系统进行第二次单目初始化。此时系统初始化`ORBmatcher`并对`mInitialFrame`和`mCurrentFrame`之间的特征点进行匹配，若匹配数目大于100，则通过`ReconstructWithTwoViews`获取相机位姿和地图点。
获取相机位姿之后，设置初始帧和当前帧位姿，最终创建初始地图`CreateInitialMapMonocular`，单目IMU初始化完成.
## ReconstructWithTwoViews
在通过两张摄影图像恢复相机位姿的过程中，分别计算**单应矩阵H**和**基础矩阵F**，并选择重投影误差较小的那个作为最终的运动估计矩阵。
在使用单应矩阵进行计算时，先使用**method of Faugeras**恢复相机运动R和t，最终在`CheckRT`中使用**三角测量**恢复3d地图点坐标。
在使用基础矩阵进行计算时，先计算出本质矩阵，再分解出R,t，最终使用**三角测量**恢复3d地图点。

