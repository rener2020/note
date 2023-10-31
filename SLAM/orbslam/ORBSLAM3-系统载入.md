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

在`SLAM.TrackMonocular`中，先存储`IMU`数据，之后通过`mpTracker->GrabImageMonocular`函数计算当前相机位姿。
具体而言，程序会以传入的图像**构建图像帧**，在构建图像帧的过程中会提取**ORB特征点**等信息。之后使用`Track()`函数进行跟踪。



