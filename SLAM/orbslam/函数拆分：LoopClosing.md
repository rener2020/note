# 函数拆分：<a class="xsj_anchor xsj_anchor_range xsj_anchor_range_start" name="xsj_1704618221767"></a>LoopClosing<a class="xsj_anchor xsj_anchor_range xsj_anchor_range_end" name="xsj_1704618221767"></a>
2024-01-05

## LoopClosing::run
1. 回环检测 NewDetectCommonRegions
2. 地图融合（在IMU模式下只有IMU初始化成功之后才融合）
	1. 如果是IMU模式，只有尺度比例在0.9-1.1之间才进行融合，且只融合yaw轴 MergeLocal2
	2. 非IMU模式：MergeLocal
3. 回环矫正
	1. IMU模式：相对位姿的旋转角度，roll、pitch不能大于0.008，yaw不大于0.349，roll、pitch强置为0进行闭环矫正 CorrectLoop()
	2. 非IMU模式： 直接进行闭环矫正 CorrectLoop、


## 闭环矫正 CorrectLoop



## MergeLocal2
1. 从“当前关键帧”地图中得到 链接区域的 关键帧+地图点
2. 取当前帧“15”个最佳共视帧，放入 局部关键帧窗口中
3. 若共视帧小于15，且下面动作未做满3次时：遍历局部关键帧窗口，找到7个最佳共视帧，若未在局部关键帧窗口中，则加入
4. 从“合并匹配帧”地图中得到 链接区域的 关键帧+地图点
5. 检测融合地图，将“合并匹配帧”地图中得到 链接区域的地图点 放入其融合地图
6. 得到当前帧的位姿的逆
7. 遍历从“当前关键帧”地图中得到 链接区域的 关键帧，并计算出与当前帧的修正关系
8. 遍历从“当前关键帧”地图中得到 链接区域的 地图点，并计算出与当前帧的修正关系
9. 关键帧和地图点修正
10. 因为地图进行了融合，所以要重新建立关键帧之间的父子连接关系
11. 融合局部窗口的地图点. 将在合并关键帧附近观察到的 MapPoints 投影到当前关键帧和使用校正姿势的邻居中。 融合重复 SearchAndFuse
12. 更新所有局部关键帧的共视连接关系 UpdateConnections
13. 进行局部窗口的BA优化 MergeInertialBA
14. 更新当前地图的关键帧和地图点
15. 利用位姿传播调整局部窗口以外的关键帧位姿，然后进行本质图优化(非单目情况才进行优化)：
16. 全局BA。imu模式下关键帧数量必须小于200，地图集只能有一个地图，不然视觉imu全局BA计算量太大 RunGlobalBundleAdjustment

## MergeLocal
和MergeLocal2基本一致，无IMU执行 LocalBundleAdjustment 而不是 MergeInertialBA

## RunGlobalBundleAdjustment
1. IMU未初始化 GlobalBundleAdjustemnt
2. IMU已初始化 FullInertialBA
3. 更新优化量


## GlobalBundleAdjustemnt
就是 BundleAdjustment


## BundleAdjustment
1. 单目边 EdgeSE3ProjectXYZ
2. 顶点 关键帧位姿顶点 VertexSE3Expmap 地图点顶点 VertexSBAPointXYZ

## 回环检测 NewDetectCommonRegions
IMU模式下如果还没进行第二次初始化则不进行回环检测
1. 若当前关键帧没有被检测到回环或融合，则寻找当前关键帧的三个回环候选帧和融合候选帧  DetectNBestCandidates
2. 对回环候选帧再进行检测，判断是否发生回环 DetectCommonRegionsFromBoW
3. 对融合候选帧再进行检测，判断是否发生融合  DetectCommonRegionsFromBoW
4. 将当前关键帧添加到关键帧数据库中


## 判断是否发生回环 DetectCommonRegionsFromBoW
1. 对于每个候选关键帧找出其共视程度最高的几个共视关键帧
2. 通过Bow寻找候选帧窗口内的关键帧地图点与当前关键帧的匹配点
3. 匹配地图点大于20，进行SIM3求解 
	1. 初始化SIM3 求解器
	2. 随机挑选3个对应的地图点，进行ransac sim3 compute
4. SIM3 收敛，取匹配帧的5个最佳共视关键帧，遍历匹配闭环帧的共视帧集合，得到匹配地图点
5. 将地图点 及 对应的关键帧 存起来
6. g2o Sim3优化求解匹配帧地图点
7. 将闭环匹配上关键帧以及相连关键帧的MapPoints 再次投影到当前关键帧进行投影匹配
8. 如果这次投影地图点大于80时，默认为最佳匹配帧已经找到了
9. 取当前帧 5个最佳共视帧 
10. 判断当前帧的共视帧与最佳匹配帧是否相关联 DetectAndReffineSim3FromLastKF 
11. 相关联则发生回环
12. 

## 判断当前帧的共视帧与最佳匹配帧是否相关联 DetectAndReffineSim3FromLastKF
1. 地图匹配点大于30：基于新的相对关系（sim3 mHessian7x7）进行优化：已匹配的地图点，相对关系
2. 如果优化后的匹配个数大于50时：按投影查找匹配点
3. 若投影匹配点大于100：相关联



## SIM3求解
见SIM3求解


## 寻找当前关键帧的三个回环候选帧和融合候选帧  DetectNBestCandidates
1. 获取当前帧的所有关键帧
2. 获取与当前关键帧相关的所有关键帧（share a word），即拥有类似的特征点
3. 只保留拥有较多类似特征点的关键帧 maxWordsNum\*0.8
4. 计算相似度成绩 mBowVec 的L1范数
5. 对候选关键帧寻找其10个共视关键帧，共同计算其成绩
6. 选出其0.75\*BestScore 的关键帧
7. 如果候选帧与当前关键帧在同一个地图里，加到回环候选帧里
8. 如果候选者与当前关键帧不再同一个地图里，添加到融合候选帧里