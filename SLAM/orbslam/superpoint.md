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
