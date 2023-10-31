---
title: ORBSLAM3-系统载入
tags: 新建,模板,小书匠
category: /小书匠/日记/2023-10
grammar_cjkRuby: true
---


以`/Examples/Monocular-Inertial/mono_inertial_euroc.cc`为例，在`main`函数中，系统现将数据载入之后，初始化SLAM类。

``` cpp?linenums
ORB_SLAM3::System SLAM(argv[1],argv[2],ORB_SLAM3::System::IMU_MONOCULAR, true);
```

之后进行系统追踪

``` cpp?linenums
```

