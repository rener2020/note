# 函数拆分：LocalMapping
2024-01-02



## LocalMapping::run
1. 处理列表中的关键帧 


## ProcessNewKeyFrame
1. 选取队列中第一个并将暂时其移出队列
2. 遍历当前关键帧的地图点
3. 如果地图点的观测是否存在当前关键帧：
	1. 不存在：地图点添加观测三部曲：添加-更新平均观测方向和深度（UpdateNormalAndDepth）-更新描述子（ComputeDistinctiveDescriptors）
	2. 存在：暂存入近期增加的地图点集合中，等待后续查验
4. 更新共视图：UpdateConnections
5. 将此关键帧加入当前地图中：AddKeyFrame

## 更新平均观测方向和深度 UpdateNormalAndDepth
1. 遍历所有地图点的可观测帧
2. 平均观测方向为，获取地图点指向可观测帧相机中心的向量，相加并取平均
3. 获取此地图点第一次创建时的关键帧
4. 计算地图点到此关键帧的深度距离（位置到光心）
5. 获取地图点在此关键帧中的金字塔层数
6. 获取尺度因子和金字塔最高层的尺度因子
7. 计算可观测到该点的距离上限下限dist\*factor