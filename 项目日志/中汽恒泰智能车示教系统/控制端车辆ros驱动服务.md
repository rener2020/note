---
title: 控制端车辆ros驱动服务
tags: 
renderNumberedHeading: true
grammar_cjkRuby: true
---


控制端的车辆`ros`驱动服务位于`app/ros/`包下，主要文件为`app/ros/vehicle.py`，其主要功能是通过`ros`服务实现与小车上各`ros`节点的通讯，从而实现读取车辆实时信息的功能。
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
以上两个变量随用随取，里面的值会由底层的数据服务实时更新。

可用函数：
- `start()`：开启本地`ros`节点
- `stop()`：关闭本地`ros`节点
- `restart()`：重启本地`ros`节点

初始化小车实时数据服务后，此服务会开启另外两个服务，为本地节点启停服务与小车数据处理服务。

本地节点启停服务的代码如下：

``` py
def vehicle_node_service(self):
        # 启动车辆节点
        while True:
            time.sleep(1)
            # 关闭优先
            if self.stop_vehicle_node.is_set():
                # 数据清空
                self.init_data()
                # 若准备关闭车辆节点
                # 车辆节点自退出
                # 查看车辆节点相关代码
                # 关闭车辆节点节点
                pass
            # 启动滞后
            if self.start_vehicle_node.is_set():
                # 若准备启动车辆节点
                # 启动车辆节点
                self.__start_vehicle_node()
                # 清空车辆节点启动信号
                self.start_vehicle_node.clear()
                # continue
                if not self.sensor_service_start:
                    start_thread_work(self.vehicle_check_sensor_service)
                    self.sensor_service_start = True
            if self.restart_vehicle_node.is_set():
                self.init_data()
                # 若准备重启车辆节点
                # 触发关闭车辆节点事件
                self.stop_vehicle_node.set()
                # 触发启动车辆节点事件
                self.start_vehicle_node.set()
                self.restart_vehicle_node.clear()
```

小车数据处理服务对应函数

其流程图如下：
```flow!
st=>start: 开始
e=>end: 结束
op0=>operation: 初始化程序
op1=>operation: 载入登录界面
op2=>operation: 用户登录
op3=>operation: 获取用户信息
op4=>operation: 根据用户信息载入主界面
op=>operation: 在
cond1=>condition: 是否开启本地ros节点？
cond2=>condition: 登录成功？
cond3=>condition: 签名是否正确？
st->op0->cond1
cond1(no)->cond1
cond1(yes)->op1->op2->cond2
cond2(no)->op2
cond2(yes)->op3->op4
op4->e
```