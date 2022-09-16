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
需要修改`c++`标准和部分代码，主要是`opencv`部分和`fstream`