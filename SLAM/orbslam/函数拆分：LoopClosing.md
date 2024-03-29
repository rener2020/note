# 函数拆分：LoopClosing
2024-01-05

## LoopClosing::run
1. 回环检测 NewDetectCommonRegions
2. 地图融合（在IMU模式下只有IMU初始化成功之后才融合）
	1. 如果是IMU模式，只有尺度比例在0.9-1.1之间才进行融合，且只融合yaw轴 MergeLocal2
	2. 非IMU模式：MergeLocal
3. 回环矫正
	1. IMU模式：相对位姿的旋转角度，roll、pitch不能大于0.008，yaw不大于0.349，roll、pitch强置为0进行闭环矫正 CorrectLoop()
	2. 非IMU模式： 直接进行闭环矫正 CorrectLoop


## 闭环矫正 CorrectLoop
1. 根据当前帧求得的相机位姿(相似变换矩阵)来求解矫正前和矫正后的相邻帧位姿变换矩阵(相似变换矩阵)
2. 将相邻关键帧的所有地图点都根据更新后的相机位姿(相似变换矩阵)重新计算地图点世界坐标
3. 进行地图点融合   将之前匹配的(在ComputeSim3()函数中计算局部地图点和当前帧的匹配)两地图点融合为同一地图点
4. 根据优化的位姿计算的地图点重新进行匹配,并融合匹配点和当前关键帧中的地图点
5. 在地图点融合之后,更新当前关键帧的共视图中各个关键帧的相连关键帧,更新连接之后,将这些相邻关键帧全部加入LoopConnections容器
6. 根据四种边(1 新检测到的回环边  2 父关键帧与子关键帧的边 3 历史回环关键帧  4 共视图边)对全局地图中的所有关键帧的位姿进行矫正
7. 根据地图点和关键帧位姿计算重投影误差对全局地图进行优化 惯性： OptimizeEssentialGraph4DoF 单目：OptimizeEssentialGraph
8. 进行一次全局BA RunGlobalBundleAdjustment

## OptimizeEssentialGraph
1. 将地图中所有关键帧的pose作为顶点添加到优化器 VertexSim3Expmap
2. 根据回环的SIM3帧连接关系添加边 EdgeSim3
3. 添加跟踪时形成的边、闭环匹配成功形成的边 
3. 对有很好共视关系的关键帧也作为边进行优化 KFs = GetCovisiblesByWeight
4. 如果是imu的话还会找前一帧做优化

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