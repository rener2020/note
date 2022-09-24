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

## 单目IMU在ORBSLAM3中运行

``` bash
rosrun ORB_SLAM3 Mono_Inertial /home/hz/share/workspace/slam/ORB_SLAM3_NOETIC/Vocabulary/ORBvoc.txt /mnt/share/workspace/slam/ORB_SLAM3_NOETIC/Examples/ROS/ORB_SLAM3/vio.yaml /usb_cam/image_raw:=/zed2i/zed_node/left/image_rect_gray /imu:=/zed2i/zed_node/imu/data
```


``` bash
rosrun ORB_SLAM3 Stereo_Inertial Vocabulary/ORBvoc.txt \
           Examples/zed2i/test_stereo_imu.yaml true \
           /camera/left/image_raw:=/zed2i/zed_node/left/image_rect_gray \
           /camera/right/image_raw:=/zed2i/zed_node/right/image_rect_gray \
           /imu:=/zed2i/zed_node/imu/data
```