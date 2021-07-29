---
title: 控制端车辆ros驱动服务
tags: 
renderNumberedHeading: true
grammar_cjkRuby: true
---


控制端的车辆`ros`驱动服务位于`app/ros/`包下，其主要功能是通过`ros`服务实现与小车上各`ros`节点的通讯，从而实现读取车辆实时信息的功能。
使用示例：

``` py
# coding:utf8
import time
from app.utils import hope, start_thread_work
from app.ros.vehicle import VehicleNodeService

# 这一行可以忽略，是防止产生僵尸进程的保护机制框架下的希望函数
# 在实际的程序中，程序刚开始运行时就在main.py文件中开启了一个线程进行这个函数的运行，不要手动运行这个函数
start_thread_work(hope)
# 实例化数据服务类
vehicle_node_service = VehicleNodeService()
# 开启数据服务
vehicle_node_service.start()
while True:
    time.sleep(0.1)
    if vehicle_node_service.is_ros_core_ok:
        print '车辆实时运行数据为:'
        print vehicle_node_service.vehicle_current_data
        print '车辆传感器状态为：'
        print vehicle_node_service.sensors
    else:
        print '车辆ros服务未启动'

```

目前车辆服务暴露出来两个可用变量接口与三个可用函数接口。
可用实时更新变量：
- `vehicle_current_data`：车辆实时数据
- `sensors`：车辆传感器状态

可用函数：
- `start()`：开启本地`ros`节点
- `stop()`：关闭本地`ros`节点
- `restart()`：重启本地`ros`节点

