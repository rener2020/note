# 函数拆分：LoopClosing
2024-01-05

1. 回环检测 NewDetectCommonRegions


## 回环检测 NewDetectCommonRegions
IMU模式下如果还没进行第二次初始化则不进行回环检测
1. 若当前关键帧没有被检测到回环或融合，则寻找当前关键帧的三个回环候选帧和融合候选帧  DetectNBestCandidates


## 寻找当前关键帧的三个回环候选帧和融合候选帧  DetectNBestCandidates
1. 获取当前帧的所有关键帧
2. 获取与当前关键帧相关的所有关键帧（share a word），即拥有类似的特征点
3. 只保留拥有较多类似特征点的关键帧 maxWordsNum\*0.8
4. 计算相似度成绩 mBowVec 的L1范数
5. 对候选关键帧寻找其10个共视关键帧，共同计算其成绩
6. 选出其0.75\*BestScore 的关键帧