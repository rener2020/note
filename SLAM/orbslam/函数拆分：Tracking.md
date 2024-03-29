# 函数拆分 ：Tracking
2023-12-20

## Tracking::Track
1. 如果LocalMAPPING线程中检测到IMU出现问题： 重置当前地图 ResetActiveMap()
2. 从Atlas中取出当前active的地图
3. 为当前帧设置偏置
4. 进行IMU预积分
5. 进行单目初始化 MonocularInitialization
6. 如果当前状态为LOST，进行重定位：Relocalization
7. 当前状态异常或在上一帧能够无法跟踪到足够的地图点（重定位失败），
	1. 如果有速度量：使用运动跟踪模型进行跟踪 TrackWithMotionModel
	2. 如果没有速度量：使用参考帧进行跟踪 TrackReferenceKeyFrame
8. 如果速度存在，先使用运动跟踪模型进行机器人定位 TrackWithMotionModel
9. 使用重定位方法再对机器人定位 Relocalization
10. 如果重定位方法成功，使用重定位结果，否则使用运动跟踪模型
11. 在跟踪得到当前帧初始位姿后，以当前局部地图跟踪得到更多的匹配，并优化当前位姿 TrackLocalMap
12. 若刚刚发生重定位且IMU已初始化：重置IMU
13. 更新图像
14. 更新地图
15. 若跟踪成功或状态为刚刚丢失,
	1. 若当前帧和上一帧的位姿不为空，构建上一帧姿态且更新运动模型，否则重置运动模型
	2. 显示当前帧位姿
	3. 清除观测不到的地图点
	4. 清除临时地图点
	5. 判断是否添加关键帧（添加）
	6. 删除外点地图点
16. 若跟踪丢失：创建新地图
17. 若当前图像帧的参考帧未设置，设置参考帧为当前图像帧的参考帧
18. 记录位姿信息，用于轨迹复现

## 局部地图跟踪 TrackLocalMap
1. 更新局部地图 UpdateLocalMap
2. 搜索局部地图点 SearchLocalPoints
3. 使用匹配点进行优化当前帧位姿
	1. IMU未初始化成功，只优化当前图像位姿 PoseOptimization
	2. 如果距离上次重定位时间比较近，IMU数量少，则不使用IMU数据，只进行位姿优化 PoseOptimization
	3. 否则，如果没有更新地图，用IMU和上一个图像帧进行位姿优化 PoseInertialOptimizationLastFrame
	4. 如果更新地图，用IMU和上一个关键帧进行位姿优化 PoseInertialOptimizationLastKeyFrame
4. 遍历当前帧的地图点，统计地图点内点的数量和外点的数量
5. 更新地图点的被观测程度和匹配内点数量
6. 根据匹配情况和系统状态确定当前跟踪成功或失败

## IMU和上一个图像帧进行位姿优化 PoseInertialOptimizationLastFrame
- 优化类型：ImuCamPose类6D位姿向量
- 顶点1，优化量：图像帧/关键帧的速度Vw
- 顶点2，优化量：图像帧/关键帧的陀螺仪偏置
- 顶点3，优化量：图像帧/关键帧的加速度计偏置
- 边：定量：地图点位姿 3D-2D的重投影误差，只优化相机位姿
- 顶点：前一图像帧的Rcw,tcw,Rbw,tbw
- 顶点：前一图像帧的速度
- 顶点：前一图像帧的陀螺仪偏置
- 顶点：前一图像帧的加速度偏置
- 边：用当前图像帧的IMU构造边，优化前一图像帧的位姿、速度、偏置、当前帧的位姿、速度
- 边：优化前后两个图像帧的加速度计偏置 在这里使用了协方差矩阵C
- 边：添加先验约束边，控制上一图像帧的优化变量尽可能小一些
- 优化，卡方检验内外点
- 更新当前图像帧的Rwb,twb,和速度
- 更新6维偏置向量
- 回覆Hessian矩阵，边缘化之前的图像帧状态并为图像帧生成新的先验
- 构造ConstraintPoseImu对象，为下一个图像帧提供先验约束
- 返回内点数量


## 搜索局部地图点 SearchLocalPoints
1. 遍历当前图像帧地图点，区分坏点和好点，好点增加观测和设置投影匹配
2. 若此地图点已被当前帧观测，则不处理
3. 查看地图点是否在此视野范围内，若在则处理
4. 进行投影匹配 SearchByProjection


## 更新局部地图 UpdateLocalMap

## 

## 进行重定位：Relocalization
1. 计算当前图像帧的词袋模型 ComputeBoW
1. 寻找与当前帧相似的候选关键帧 DetectRelocalizationCandidates
2. 遍历候选关键帧，使用词袋模型在候选关键帧和当前帧进行3d-2d匹配
3. 设置MLPNP求解器
4. 使用ransac算法对MLPNP进行五次迭代
5. 进行一次优化图像帧的位姿，利用3D-2D重投影误差 PoseOptimization
6. 优化后如果内点不够，则再次搜索3d-2d匹配，最多重复两次

## 寻找与当前帧相似的候选关键帧 DetectRelocalizationCandidates
1. 计算关键帧和当前帧描述子一致个数、
2. 取单关键帧最大描述子描述个数的0.8作为min，计算大于min的score
3. 在共视关键帧中寻找匹配度最好的帧，并累计成绩当作最好帧成绩
4. 返回成绩大于0.75\*bestscore的帧

##  重置当前地图 ResetActiveMap
只是重置了一个变量

## IMU预积分 PreintegrateIMU()
1. 准备预积分IMU数据
2. 计算中值预积分数据
3. 相对于上一关键帧进行IMU预积分 IntegrateNewMeasurement
4. 相对于上一图像帧进行IMU预积分 IntegrateNewMeasurement
5. 设置已积分状态

## 进行IMU预积分 IntegrateNewMeasurement
1. 计算去除偏置之后的加速度、角速度
2. 计算两帧之间的平均加速度和角速度
3. 使用未更新的dR更新dP和dV
4. 计算位置、速度相对于两个偏差的雅可比
5. 更新角度增值
6. 更新协方差
7. 计算旋转相对于偏差的雅可比

## 检查替换地图点 CheckReplacedInLastFrame
局部建图线程可能会对原有地图点进行替换，在这里将地图点替换为新地图点


## 跟踪参考关键帧 TrackReferenceKeyFrame
若运动模型是空的且IMU未初始化，或者刚完成重定位，则跟踪参考关键帧
1. 搜索匹配当前参考关键帧和当前帧的特征点
2. 特征点>15
3. 上一帧位姿作为当前帧初始位姿
4. PoseOptimization
5. 清除外点




## 只优化图像帧的位姿，利用3D-2D重投影误差 PoseOptimization

使用g2o优化图像帧的位姿
1. 设置线性求解器
2. 设置块求解器
3. 设置优化方式
4. 求解器初始化完成
5. 添加顶点：当前图像帧六维位姿（优化变量）
6. 定义一元边：3D-2D匹配误差边
7. 定义信息矩阵，层数越高，观测误差可能越大，则设置越小的信息矩阵，减少对整体系统的影响
8. 设置鲁棒核函数
9. 匹配点不足，放弃优化
10. 4次迭代优化，每次迭代次数为10，在迭代的过程中根据误差动态调整内点外点
11. 第二次优化后因为误差较小，去除误差核函数
12. 得到优化后的图像帧位姿


## 跟踪恒速模型 TrackWithMotionModel
若运动模型不是空的或IMU已经初始化，且不是刚完成重定位，则跟踪参考关键帧
1. 设置上一个图像帧位置 UpdateLastFrame
2. 根据IMU或者恒速模型得到当前帧的初始位姿  
2. IMU刚初始化不久：用IMU来估计位姿 PredictStateIMU 返回
3. IMU未初始化或者时间很久： 用恒速模型得到当前图像帧的初始位姿 $mVelocity*mLastFrame.mTcw$
4. 投影法特征匹配 SearchByProjection
5. 利用3D-2D重投影误差优化位姿 PoseOptimization

## 根据IMU或者恒速模型得到当前帧的初始位姿  PredictStateIMU
1. 提取上一帧位姿
2. 计算当前帧$R$
	1. $R = R_1R_2$
	2. $t = t +vt - 0.5gt^2 + R\Delta t$
	3. $v = v + gt + R\Delta v$

## 投影法特征匹配 SearchByProjection
1. 将上一帧的地图点反投影到当前帧像素平面上
2. 在搜索半径$th$内搜索特征点
3. 以描述子选择最佳匹配
4. 检查匹配方向
5. 返回匹配3d-2d特征点