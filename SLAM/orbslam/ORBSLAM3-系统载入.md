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

在`SLAM.TrackMonocular`中，先存储`IMU`数据，之后
