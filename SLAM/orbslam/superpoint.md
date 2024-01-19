# SUPERPOINT
2024-01-19


 ## 训练集生成：
 **自适应单应变换**
对图像施加不同的单应变换，可以模拟不同的相机视角看到图像
![enter description here](./images/1705628697513.png)


## 基于Encoder-Decoder的网络结构

- input：图像
- Encoder：卷积神经网络
- Decoder：
	- Interest Point： 特征点位置描述，softmax，NMS，reshape
	- Descriptor：描述子生成，插值，L2-Norm

![enter description here](./images/1705629033218.png)
# SURPERGLUE
## 输入：
两组图像的特征点坐标和描述子

## 网络模型

![enter description here](./images/1705629551904.png)
SUPERGLUE主要由基于注意力机制的图网络层和最优匹配层组成。
其中注意力机制网络先对输入描述子和坐标通过一个Encoder聚合，再送到带有注意力机制的网络层进行聚合，最终得到matching descriptors，接着把匹配问题建模成运输问题，利用经典算法sinkhorn求解。
