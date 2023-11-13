# LocalMapping 
2023-11-13


LocalMapping的入口函数是`LocalMapping::Run`函数
1. 处理列表中的关键帧`ProcessNewKeyFrame`，为地图点添加关键帧作为观测，更新地图点的平均观测方向和深度，更新地图点的最佳描述子，更新关键帧的共视图和添加关键帧到地图。
2. 