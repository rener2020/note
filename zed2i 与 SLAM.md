---
title: zed2i 与 SLAM
category: 2022-09-24
renderNumberedHeading: true
grammar_cjkRuby: true
---



## 相机标定
ZED2i相机自己发布的话题中，带有原始图像输出和矫正畸变之后的图像输出。以左目相机为例：
- 原始图像话题：`/zed2i/zed_node/left_raw/image_raw_gray`
- 矫正畸变之后话题：`/zed2i/zed_node/left/image_rect_gray`

其相机参数可以不用标定，我们使用矫正畸变之后的图像输出，其相机参数话题为：`/zed2i/zed_node/left/camera_info`
相机参数如下：

``` 
height: 360
width: 640
distortion_model: "plumb_bob"
D: [0.0, 0.0, 0.0, 0.0, 0.0]
K: [256.2857971191406, 0.0, 313.02838134765625, 0.0, 256.2857971191406, 181.3371124267578, 0.0, 0.0, 1.0]
R: [1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0]
P: [256.2857971191406, 0.0, 313.02838134765625, 0.0, 0.0, 256.2857971191406, 181.3371124267578, 0.0, 0.0, 0.0, 1.0, 0.0]

```

## IMU标定
IMU的参数可以自行标定。ZED2i给出了矫正之后的IMU数据，先使用矫正之后的IMU数据进行实验。
其话题为：`/zed2i/zed_node/imu/data`，IMU参数设置为`0`。

## 相机加IMU联合标定。
相机加IMU联合标定主要标定的是相机与IMU之间的位姿转换矩阵$T$，其可以从ZED2i相机的ros话题中获取。其话题为：`/zed2i/zed_node/left_cam_imu_transform`


## 运行SLAM 
### ORBSLAM3
使用以下命令：
``` bash
rosrun ORB_SLAM3 Mono_Inertial /home/hz/share/workspace/slam/ORB_SLAM3_NOETIC/Vocabulary/ORBvoc.txt /mnt/share/workspace/slam/ORB_SLAM3_NOETIC/Examples/ROS/ORB_SLAM3/vio.yaml /usb_cam/image_raw:=/zed2i/zed_node/left/image_rect_gray /imu:=/zed2i/zed_node/imu/data
```

### VINS
使用以下命令：
``` bash
rosrun ORB_SLAM3 Stereo_Inertial Vocabulary/ORBvoc.txt \
           Examples/zed2i/test_stereo_imu.yaml true \
           /camera/left/image_raw:=/zed2i/zed_node/left/image_rect_gray \
           /camera/right/image_raw:=/zed2i/zed_node/right/image_rect_gray \
           /imu:=/zed2i/zed_node/imu/data
```