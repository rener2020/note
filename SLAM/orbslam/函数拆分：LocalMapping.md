# 函数拆分：LocalMapping
2024-01-02



## LocalMapping::run
1. 处理列表中的关键帧  ProcessNewKeyFrame
2. 对mlpRecentAddedMapPoints中的地图点进行检测和剔除 MapPointCulling
3. 通过三角化恢复新的地图点 CreateNewMapPoints
4. 融合当前关键帧和相邻关键帧中重复的地图点 SearchInNeighbors

如果是IMU模式，且IMU初始化已完成，则判断IMU运动距离是否足够
- 足够：局部惯性BA LocalInertialBA
- 不够：局部BA LocalBundleAdjustment

若IMU模式且还没进行初始化，则进行IMU初始化  InitializeIMU

1. 删除冗余关键帧 KeyFrameCulling 若此关键帧中90%的地图点都至少被3个其他关键帧看到，则认为这个关键帧是冗余关键帧
2. 在第5秒 第10秒 再次初始化IMU InitializeIMU
3. 在第100秒之前每隔10s优化一次尺度和重力方向
4. 将当前关键帧加入回环检测 InsertKeyFrame 


## IMU初始化  InitializeIMU
1. 载入时间序列上所有的关键帧
2. 初始化IMU偏置为0
3. 计算帧速度初值和重力方向
4. 使用当前z方向和重力方向计算初始旋转矩阵
5. 进行惯性优化 InertialOptimization
6. 使用优化后的初始帧旋转矩阵进行系统矫正
7. 更新tracking线程中存储的相对位姿 UpdateFrameIMU
8. 进行一次全局惯性BA FullInertialBA
9. 再次更新相对位姿 UpdateFrameIMU

## 全局惯性BA FullInertialBA
- 获取所有的关键帧和地图点
- 设置关键帧顶点 位姿 VertexPose 速度 VertexVelocity 陀螺仪偏置 VertexGyroBias 加速度偏置 VertexAccBias
- 添加连接惯性顶点 使用惯性边 EdgeInertial 陀螺仪边 EdgeGyroRW 加速度边 EdgeAccRW （C在这里使用作为信息矩阵）
- 设置先验边 加速度先验边  EdgePriorAcc 陀螺仪先验边 EdgePriorGyro
- 设置地图点顶点 VertexSBAPointXYZ 和帧位姿边 VertexPose  用单目边连接 EdgeMono 投影误差
- 通过优化变量更新系统变量



## 更新相对位姿 UpdateFrameIMU
- 恢复相关帧之间的相对尺度
- 更新帧偏置
- 设置帧速度

## 矫正 ApplyScaledRotation
- 使用优化量矫正旋转位姿和使用尺度优化量恢复真实速度、位置等坐标系和尺度
- 使用优化量矫正地图点的旋转位姿和尺度

## 惯性优化 InertialOptimization
1. 保持关键帧的位姿不变，优化关键帧的速度 VertexPose VertexVelocity
2. 设置陀螺仪和加速度计偏置节点 VertexGyroBias VertexAccBias
2. 添加关于加速度计与陀螺仪偏置的边，保证第一帧的偏置为0 EdgePriorAcc EdgePriorGyro
3. 添加关于重力和尺度的节点  VertexGDir VertexScale
4. 将时间域上每两个关键帧之间设置位置、速度、加速度、角速度、重力、尺度顶点
5. 优化

## 局部BA LocalBundleAdjustment
1. 获得当前关键帧的共视关键帧，添加共视关键帧到局部关键帧列表中
2. 遍历局部关键帧列表，添加可以使用的地图点
3. 遍历局部地图点，在lFixedCameras中存入可观测到地图点但不是局部关键帧的关键帧，不进行优化
4. 判断固定关键帧的个数，保证至少2个关键帧是固定的。避免尺度漂移
5. 遍历局部关键帧列表，设置局部关键帧顶点 VertexSE3Expmap
6. 添加关键帧顶点的固定顶点 VertexSE3Expmap
7. 遍历局部地图点，添加顶点 VertexSBAPointXYZ 遍历此地图点的观测 边一边连接此地图点，一边连接KF EdgeSE3ProjectXYZ
8. 外点检测，剔除地图点
9. 遍历所有的局部关键帧，更新关键帧位姿
10. 遍历地图点，更新地图点位姿

## 局部惯性BA LocalInertialBA
先确定待优化的关键帧
1. 加入当前关键帧的前nd个关键帧，被min控制
2. 将这些关键帧的地图点加入lLocalMapPoints
3. 共视关键帧中地图点加入lLocalMapPoints
3. 固定待优化关键帧的最开始一帧的上一帧，不存在则固定其本身
4. 将lLocalMapPoints中地图点的观测帧中未被加入优化的帧加入固定关键帧
5. 建立关于关键帧的节点，其中包括，位姿，速度，以及两个偏置
5. 建立关于共视关键帧的节点


构造优化器，正式开始优化
1. 建立局部时间相关关键帧的边，其中包括，位姿，速度，以及两个偏置 VertexPose VertexVelocity VertexGyroBias VertexAccBias
2. 建立局部视觉相关关键帧的边，其中包括位姿 VertexPose
3. 建立固定关键帧相关关键帧的边，其中包括位姿，速度，以及两个偏置 VertexPose VertexVelocity VertexGyroBias VertexAccBias
4. 建立惯性约束，这里的惯性约束指的是IMU计算出来的位置、速度、加速度等量，根据两帧之间的这些量建立约束，并且对惯性边降权，避免由于固定变量而累积错误
5. 给地图点添加单目视觉边
6. 删除当前帧外点
7. 优化结果系统赋值
	

## 融合当前关键帧和相邻关键帧中重复的地图点 SearchInNeighbors
融合关键帧选择条件：
1. 当前关键帧共视关键帧中未融合的关键帧作为候选关键帧
2. 候选关键帧共视关键帧中未融合的关键帧
3. 如果是IMU模式，递归其前置关键帧如候选关键帧到20帧

用当前关键帧的地图点与候选关键帧的地图点进行融合，融合策略为：
将地图点投影到关键帧中进行匹配和融合
1.如果地图点能匹配关键帧的特征点，并且该点有对应的地图点，那么选择观测数目多的替换两个地图点
2.如果地图点能匹配关键帧的特征点，并且该点没有对应的地图点，那么为该点添加该投影地图点

再用候选关键帧的地图点与当前关键帧进行融合，之后
1. 更新当前关键帧的最小描述子ComputeDistinctiveDescriptors 和平均观测方向  UpdateNormalAndDepth
2. 更新当前关键帧的共视图 UpdateConnections



## 通过三角化恢复新的地图点 CreateNewMapPoints
1. 获取共视程度最高的n个关键帧
2. 如果是IMU模式，则添加更多的关键帧存入附近关键帧，添加逻辑为：将不大于n个关键帧的前一关键帧加入获取关键帧
3. 如果基线长度/场景深度<0.01，则跳过
4. 计算当前关键帧和周围关键帧的基础矩阵F
5. 对当前处理的关键帧和周围一关键帧进行三角化搜索特征 SearchForTriangulation
6. 对每对成功匹配点三角化（线性三角化SVD）生成地图点
7. 检查三角化，是否正面、投影误差
8. 检查尺度比例与帧金字塔层尺度比例是否一致
9. 构建新3d点

## 三角化搜索特征 SearchForTriangulation
搜索特征点对应关系
1. 相机1在2上的投影距离匹配特征点不要太近
2. 匹配距离检查
3. 极线距离检查

## 地图点进行检测和剔除 MapPointCulling
剔除逻辑
1. 坏点
2. 已经被观测到帧/应该被观测到帧 < 0.25
3. 被超过两个关键帧跟踪但是观测到此地图点帧个数未达到阈值

## ProcessNewKeyFrame
1. 选取队列中第一个并将暂时其移出队列
2. 遍历当前关键帧的地图点
3. 如果地图点的观测是否存在当前关键帧：
	1. 不存在：地图点添加观测三部曲：添加-更新平均观测方向和深度（UpdateNormalAndDepth）-更新描述子（ComputeDistinctiveDescriptors）
	2. 存在：暂存入近期增加的地图点集合中，等待后续查验
4. 更新共视图：UpdateConnections
5. 将此关键帧加入当前地图中

## 更新平均观测方向和深度 UpdateNormalAndDepth
1. 遍历所有地图点的可观测帧
2. **平均观测方向**为获取地图点指向可观测帧相机中心的向量，相加并取平均
3. 获取此地图点第一次创建时的关键帧
4. 计算地图点到此关键帧的深度距离（位置到光心）
5. 获取地图点在此关键帧中的金字塔层数
6. 获取尺度因子和金字塔最高层的尺度因子
7. 计算**可观测到该点的距离上限下限** = dist\*factor

## 更新描述子 ComputeDistinctiveDescriptors
一个地图点可以被n个关键帧观测到，在每个关键帧中都有一个其orb描述子，获取其一个与其他描述子距离最小的描述子作为代表描述子

## 更新共视图：UpdateConnections
1. 获取当前关键帧地图点，根据地图点观测度重合程度与其他关键帧建立边
2. 剔除小于阈值的边，只保留大于阈值或者阈值之下最大的边
3. 如果未初始化，则初始化为连接权重最大的边