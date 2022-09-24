---
title: 相机与IMU标定
category: 2022-09-16
renderNumberedHeading: true
grammar_cjkRuby: true
---

相机使用ros包进行标定 
``` bash
roslaunch usb_cam usb_cam-test.launch
rosrun camera_calibration cameracalibrator.py --size 11x8 --square 0.06 image:=/usb_cam/image_raw camera:=/usb_cam
```

IMU使用`imu_utils`进行标定，安装`imu_utils`之前需要安装`code_utils`。

``` bash
https://github.com/gaowenliang/imu_utils
https://github.com/Praqma/code-utils
```
需要修改`c++`标准和部分代码，主要是`opencv`部分和`fstream`代码

随机误差

``` ebnf
imu_utils
```

确定误差


使用kalibr标定imu


## 相机
topic：`/usb_cam/image_raw`


orbslam ros

``` bash
rosrun ORB_SLAM3 Mono_Inertial /home/hz/share/workspace/slam/ORB_SLAM3_NOETIC/Vocabulary/ORBvoc.txt /mnt/share/workspace/slam/ORB_SLAM3_NOETIC/Examples/ROS/ORB_SLAM3/vio.yaml 
```
vins ros

``` bash

```


## 标定

``` bash
rosrun kalibr kalibr_calibrate_imu_camera --target /data/clib/checkerboard.yaml  --cam /data/clib/cam.yaml --imu /data/clib/imu.yaml --bag /data/clib/stereo_imu_biaoding_2022-09-16-19-33-04.bag 
```

## 新相机

``` routeros
catkin_make -DCMAKE_BUILD_TYPE=Release -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-11.7
```

# ZED2i相机标定
使用*kalibr*就行相机的标定。
## 安装标定工具