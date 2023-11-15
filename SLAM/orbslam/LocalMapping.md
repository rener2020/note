# LocalMapping 
2023-11-13


LocalMapping的入口函数是`LocalMapping::Run`函数，在函数中，首先检查是否存在新的关键帧，若有就进行以下操作
1. 处理关键帧：处理列表中的关键帧`ProcessNewKeyFrame`，为地图点添加关键帧作为观测，更新地图点的平均观测方向和深度，更新地图点的最佳描述子，更新关键帧的共视图和添加关键帧到地图。
2. 地图点剔除：剔除不好的地图点
3. 构建新地图点：通过三角化恢复新的地图点


处理完一个关键帧之后，若没有新关键帧则融合当前关键帧和相邻关键帧之间重复的地图点，若
4. 相邻关键帧融合：
5. 局部惯性BA：`Optimizer::LocalInertialBA`，当惯性数据有效
6. 局部BA：`Optimizer::LocalBundleAdjustment`，当惯性数据无效
7. IMU初始化：`InitializeIMU`当使用IMU且未初始化
8. 删除冗余关键帧：`KeyFrameCulling`
9. 