---
title: Kinect V2 neotic
tags: 新建,模板,小书匠
category: /小书匠/日记/2023-04
emoji: "😙"
grammar_cjkRuby: true
---

https://www.reddit.com/r/ROS/comments/uj1auq/set_up_kinect_v2_with_ros_noetic/

首先安装这个
https://github.com/OpenKinect/libfreenect2#linux

You have to install those packages: https://github.com/OpenKinect/libfreenect2#linux https://github.com/paul-shuvo/iai_kinect2_opencv4#install <- There's an error in the instructions, replace git clone https://github.com/code-iai/iai_kinect2.git with git clone https://github.com/paul-shuvo/iai_kinect2_opencv4.git!


编译失败
 #error PCL requires C++14 or above
https://blog.csdn.net/handily_1/article/details/122421305