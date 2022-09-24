---
title: zed2i 与 SLAM
category: 2022-09-24
renderNumberedHeading: true
grammar_cjkRuby: true
---

## 相机标定
相机不用标定
其参数在：

``` bash
left/camera_info, right/camera_info, left_raw/camera_info and right_raw/camera_info
```

## 在ORBSLAM3中运行

``` bash
rosrun ORB_SLAM3 Stereo_Inertial Vocabulary/ORBvoc.txt \
           Examples/zed2/test_stereo_imu.yaml true \
           /camera/left/image_raw:=/zed2/zed_node/left/image_rect_gray \
           /camera/right/image_raw:=/zed2/zed_node/right/image_rect_gray \
           /imu:=/zed2/zed_node/imu/data
————————————————
版权声明：本文为CSDN博主「slender-」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/slender_1031/article/details/115030053
```