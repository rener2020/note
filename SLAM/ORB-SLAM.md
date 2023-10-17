ORBSLAM3


# 线程分解

- TRACKING
- LOCAL MAPPING
- LOOP & MAP MERGE & FULL BA

  ![enter description here](./images/1697531249235.png)
  
### 信号同步
上一帧图像的时间戳 < IMU测量量的时间戳 <= 当前图像时间戳  $\to$ 存入IMU测量vector
  
**视觉和IMU如何粗对齐？**