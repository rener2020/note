

# SIM3求解

2024-1-5

[原文论文](https://web.stanford.edu/class/cs273/refs/Absolute-OPT.pdf)

## 问题描述
求两个拥有两个坐标描述（尺度不同）下的三个对应点$\{p_{l1},p_{l2},p_{l3}\}$和$\{p_{r1},p_{r2},p_{r3}\}$直接的坐标转换关系与尺度
$$p_r = sR_l^rp_l + t$$

## 旋转矩阵
随机取坐标描述 $O_1$下一向量
$$v_1 = p_{l2} - p_{l1}$$
当作的$x$轴方向一向量，其取单位向量为 $x_l$，则其$y$轴方向一向量取为
$$v_2 = p_{l3} - p_{l1} - (p_{l3} - p_{l1})\cdot v_1$$
取其单位向量为$y_l$，则$z$轴单位向量为：
$$z_l = x_l \times y_l$$
得到相对与世界坐标系的坐标旋转描述$R_w^l = [x_l,y_l,z_l]$和$R_w^r = [x_r,y_r,z_r]$，于是得到两个坐标之间的旋转关系：
$$R_l^r = {R_w^l}^T R_w^r$$
## 平移向量
$$
\begin{align}
\bar{p_l} &= \frac{1}{n}\sum p_l \\
\bar{p_r} &= \frac{1}{n}\sum p_r
\end{align}
$$
设：
$$
\begin{align}
\hat{p_l} &=  p_l - \bar{p_l}\\
\hat{p_r} &=  p_r - \bar{p_r}
\end{align}
$$
则有：
$$e = \hat{p_r} - sR\hat{p_l}-\hat{t}$$
其实上面的推导不重要，给出结论：一眼看出，已知尺度因子$s$时，
$$t = \bar{p}_l - sR\bar{p}_r$$
## 尺度因子

误差函数：
$$e = \hat{p_r} - sR\hat{p_l}-\hat{t}$$
最小二乘误差：
$$ \begin{align}
||e||^2 &= \sum||\hat{p_r} - sR\hat{p_l}-\hat{t}||^2 \\
&= \sum||\hat{p}_r- sR\hat{p}_l||^2 - 2\hat{t}(\hat{p}_r- sR\hat{p}_l) + \hat{t}^2 \\
&= \sum||\hat{p}_r||^2-2s\hat{p}_rR\hat{p}_l + s^2||R\hat{p}_l||^2+\hat{t}^2
\end{align}$$
有$\hat{t}=0$，且$||R\hat{p}||^2 = ||\hat{p}||^2$，有：
$$||e||^2 = \sum||\hat{p}_r||^2-2s\hat{p}_rR\hat{p}_l + s^2||\hat{p}_l||^2$$
写作：
$$||e||^2 = S_r - 2sD + s^2S_l = 0$$
解出：
$$s = \sum\hat{p}_rR\hat{p}_l \big{/}\sum||p_r||^2$$
但是使用这个结果和系统对称性来求取对称参数时，其含有旋转变换，在尺度上的参数化不能很好的体现对称性，不优美，也不利于后续参数求解，改进一下误差函数：
$$ \begin{align}
||e||^2 &=  \sum||\frac{1}{\sqrt{s}} \hat{p}_r- \sqrt{s}R\hat{p}_l||^2 \\
\end{align}$$
可求解出：
$$s = \sqrt{\sum||\hat{p}_r||^2 \big{/} \sum||\hat{p}_l||^2} $$
这个结果不含有旋转因子，而且其旋转误差和尺度误差相互剥离了


## 计算与优化
对于优化后的误差方程来说，最小误差就是最大化：
$$
\sum\hat{p}_rR\hat{p}_l
$$
使用四元数求解，设$q$为表示旋转的四元数，则目标为最大化：
$$\sum q\hat{p}_l\hat{p}_rq = \sum q \mathbf{IR}_l\mathbf{IR}_rq = \sum qNq$$

而使其最大化的四元数是矩阵$N$最大特征值的特征向量，设：
$$M = \sum \hat{p}_l\hat{p}_r^T = \begin{bmatrix}S_{xx}&S_{xy}&S_{xz}\\S_{yx}&S_{yy}&S_{yz}\\S_{zx}&S_{zy}&S_{zz}\end{bmatrix}$$

则
$$
N = \begin{bmatrix}(S_{xx}+S_{yy}+S_{zz})&S_{yz}-S_{zy}&S_{zx}-S_{xz}&S_{xy}-S_{yx}\\S_{yz}-S_{zy}&(S_{xx}-S_{yy}-S_{zz})&S_{xy}+S_{yx}&S_{zx}+S_{xz}\\S_{zx}-S_{xz}&S_{xy}-S_{yx}&(-S_{xx}+S_{yy}-S_{zz})&S_{yz}+S_{zy}\\S_{xy}-S_{yx}&S_{zx}+S_{xz}&S_{yz}-S_{zy}&(-S_{xx}-S_{yy}+S_{zz})\end{bmatrix}
$$

计算可得到四元数，之后按公式求解$t$和$s$即可


