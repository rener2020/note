---
title: 相机与IMU标定
category: 2022-09-16
renderNumberedHeading: true
grammar_cjkRuby: true
---

相机使用ros包进行标定 
``` bash
roslaunch usb_cam usb_cam-test.launch
rosrun camera_calibration cameracalibrator.py --size 11x8 --square 0.03 image:=/usb_cam/image_raw camera:=/usb_cam
```

