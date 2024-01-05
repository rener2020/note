# 函数拆分：LoopClosing
2024-01-05

1. 回环检测 NewDetectCommonRegions


## 回环检测 NewDetectCommonRegions
IMU模式下如果还没进行第二次初始化则不进行回环检测
1. 若当前关键帧没有被检测到回环或融合，则寻找当前关键帧的三个回环候选帧和融合候选帧


