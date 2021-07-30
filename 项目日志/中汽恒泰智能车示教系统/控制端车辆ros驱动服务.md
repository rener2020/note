---
title: 控制端车辆ros驱动服务
tags: 
renderNumberedHeading: true
grammar_cjkRuby: true
---

### VehicleNodeService

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
					# 开启传感器状态数据服务
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

小车数据处理服务对应函数为`start_processing_vehicle_node_data`，其流程图如下：

```flow!
st=>start: 开始
e=>end: 结束
op0=>operation: 从本地ros节点读取小车数据
op1=>operation: 进行数据更新
cond=>condition: 程序退出？

st->op0->op1->cond
cond(no)->op0
cond(yes)->e
```

小车传感器数据服务对应函数为`vehicle_check_sensor_service`，其流程图如下：
```flow!
st=>start: 开始
e=>end: 结束
op0=>operation: 载入本地配置文件
op1=>operation: 访问车端服务获取当前传感器
op2=>operation: 更新传感器状态信息
cond=>condition: 程序退出？
st->op0->op1->op2->cond
cond(no)->op0
cond(yes)->e
```

其中更新传感器信息代码如下：

``` py
            state, result = vehicle_communication.communicate({
                'action': "car_sys_proc",
            })
			vehicle_server_processes = result['data']
            for process_name in self.sensors:
                if process_name not in vehicle_server_processes:
                    # 如果车端服务设置该传感器不使能
                    self.sensors[process_name]['sensor_status'] = False
                    # 不往下检查，默认传感器数据状态为否
                    self.sensors[process_name]['data_status'] = False
                    continue
                self.sensors[process_name]['sensor_status'] = True
                topic_name = self.sensors[process_name]['topic']
                if topic_name in self.sensors_log_time and time.time() - self.sensors_log_time[topic_name] < 2:
                    # 如果存在该传感器记录数据且两秒内该传感器有数据，则说明该传感器激活且状态正常
                    sim_log(self.sensors[process_name])
                    self.sensors[process_name]['data_status'] = True
                    self.sensors[process_name]['sensor_status'] = True
                else:
                    self.sensors[process_name]['data_status'] = False
```

### Vehicle
基于小车数据的服务都依赖于本地`ros`节点与车端`ros`节点的通讯过程，这一通讯过程的实现可见`Vehicle`类。因为`ros`节点需要在主线程中初始化，所以开启一个进程来运行`ros`节点，进程间使用队列进行通信。进行实例化`Vehicle`类的代码位于`VehicleNodeService`中，如下：

``` py
    def __start_vehicle_node(self):
		...
		...
        def start_vehicle_node():
            # 启动车辆节点函数
            Vehicle(self.vehicle_node_data_queue, self.start_vehicle_node, self.stop_vehicle_node)

        # 启动进程
        start_progress_work(start_vehicle_node)
        ...
		...
```

`Vehicle`类的核心功能为以下三个：
- 开启`ros`节点：
	``` py
	# 开启ros节点
	rospy.init_node('vehicle', anonymous=True, disable_signals=True)
	```
- 订阅`ros`话题，以小车电压话题为例：
	``` py
	# 电压
	rospy.Subscriber('/voltage', Float32, callback=self.voltage_action)
	```
- 获取话题数据，以小车电压数据为例：
	``` py
	def voltage_action(self, data):
        self.vehicle_node_data_queue_put({
            'state': True,
            'detail': '',
            'type': 'voltage',
            'data': data.data
        })
	```
	
`Vehicle`类实例与`VehicleNodeService`实例以队列的方式进行通讯，`Vehicle`实例只进行入队列操作，`VehicleNodeService`实例只进行出队列操作，`Vehicle`中操作队列的函数如下：

``` py
    def vehicle_node_data_queue_put(self, content):
        if self.is_queue_full:
            # 队列满标志，当其置True时不允许其他线程对队列进行操作
            sim_log("车辆节点通讯队列满，抛弃内容，直接返回")
            return
        if self.vehicle_node_data_queue.full():
            # 置队列满标志为True，不允许其他线程对队列进行操作
            self.is_queue_full = True
            try:
                while not self.vehicle_node_data_queue.empty():
                    self.vehicle_node_data_queue.get_nowait()
            except Exception, e:
                sim_log("车辆节点通讯队列冲突,取队列出错：{}".format(str(e.message)))
            finally:
                self.is_queue_full = False
        try:
            self.vehicle_node_data_queue.put_nowait(content)
        except Exception, e:
            sim_log("车辆节点通讯队列冲突，入队列出错：{}".format(str(e.message)))
```

