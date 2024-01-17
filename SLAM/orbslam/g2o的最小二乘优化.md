# g2o的最小二乘优化
2024-01-08

## 优化
考虑高斯牛顿法：
$$F(x) = ||e(x) + J\Delta x||^2_2$$
加入信息矩阵：
$$F(x) = [e(x) + J\Delta x]^T\Omega[e(x)+J\Delta x] = e(x)^T\Omega e(x) + 2e(x)^T\Omega J \Delta x + \Delta x ^TJ^T\Omega J \Delta x$$
对$\Delta x$求偏导等于$0$：
$$2e(x)^T\Omega J + 2J^T\Omega J \Delta x = 0$$
求得$\Delta x$，如果是LM方法，则：
$$2e(x)^T\Omega J + (2J^T\Omega J + \lambda I)\Delta x  = 0$$
## 参数替换理论
使用扰动模式使得雅可比矩阵存在，将对原始不可导参数的求导转换为对于扰动的求导使得雅可比存在

## 稀疏的系统结构

舒尔补消元
$$
\begin{align}
&\begin{cases}
\begin{bmatrix}
H_1 &  H_2 \\
H_2^T & H_3 
\end{bmatrix}[x_1,x_2]^T &= [b_1,b_2] \to \\
\begin{bmatrix}
H_1 - H_2 H_3^{-1}H_2^T & 0 \\
H_2^T & H_3
\end{bmatrix}[x_1,x_2]^T &= [b_1 - H_2H_3^{-1}b_2,b_2]^T
\end{cases}  \to \\
&\begin{cases}
(H_1 - H_2H_3^{-1}H_2^T)x_1 &= b_1 - H_2H_3^{-1}b_2 \\
H_3x_2 &= b_2 - H_2^Tx_1
\end{cases}
\end{align}
$$
直接将复杂矩阵的求逆变为一个很小的矩阵求解和对角阵求逆






## 残差
### 重投影误差
$$res(x) = s_ip_i - KT_W^CP_i$$
### 对极几何角度残差
相机中心在$C$，空间地图点位置在$M_C$，归一化球面中$m = \frac{M_C}{||M_C||}$

$$m_2^TEm_1 = 0$$

 

## IMU 残差
理想值：
$$
\begin{align}
&\begin{cases}
& R_j = R_i\prod\exp(\tilde{\omega} - b - \eta)\Delta t \\
& v_j = v_i + g\Delta t + \sum R_k(\tilde{a} -b -\eta)\Delta t \\
& p_j = p_i + \sum \big[ v\Delta t + \frac{1}{2} g\Delta t^2 + \sum \frac{1}{2} R(\tilde{a} -b -\eta)\Delta t^2  \big] 
\end{cases} \to \\
& \begin{cases}
&\Delta R_{ij} \dot{=} R_i^TR_j = \prod \exp \big( \tilde{\omega} -b- \eta \big)\Delta t  \\
&\Delta v_{ij} \dot{=} R_i^T(v_j - v_i - g\Delta t_{ij}) = \sum_{k=i}^{j-1}\Delta R_{ik}(\tilde{a}_k - b_k - \eta_k)\Delta t \\
&\Delta p_{ij} \hat{=} R_i^T(p_j - p_i -v_i\Delta t_{ij} - \frac{1}{2}g\Delta  t_{ij}^2) = \sum_{k=i}^{j-1}\big[ \Delta v_{ik}\Delta t + \cfrac{1}{2}\Delta R_{ik}(\tilde{a} - b - \eta)\Delta t^2 \big]
\end{cases}
\end{align}
$$

### 分离噪声
$$
\begin{align}
\Delta R_{ij} &= \Delta\tilde{R}_{ij}\prod\limits_{k=i}^{j-1}\exp(-\Delta\tilde{R}_{k+1,j}^T\cdot J_r^k\eta \Delta t) \\
 &= \Delta\tilde{R}_{ij}\exp(-\delta \phi)
\end{align}
$$
其中$\Delta\tilde{R}_{ij}$ 为预积分测量值，由陀螺仪的测量值和对陀螺仪的bias的估计得到，$\exp(\delta\phi)$为其测量噪声

$$
\begin{align}
\Delta v_{ij} &= \sum \Delta\tilde{R}_{ik}(\tilde{a} - b)\Delta t + \sum \big[\Delta\tilde{R}_{ik}[\tilde{a} - b][\delta]\Delta t - \Delta\tilde{R}_{ik}\eta\Delta t\big] \\
& = \Delta \tilde{v}_{ij} - \delta v_{ij}
\end{align}
$$
其中$\tilde{v}_{ij}$为预积分测量值，由IMU测量值和对bias的估计得到，$\delta v_{ij}$为其测量噪声

$$
\begin{align}
\Delta p_{ij} &= \sum\big[ \Delta\tilde{v}_{ik}\Delta t + \cfrac{1}{2}\Delta\tilde{R}_{ik}(\tilde{a} - b)\Delta t^2 - \cfrac{1}{2}\Delta\tilde{R}\eta\Delta t^2 - \delta v_{ik}\Delta t \big] \\
&  = \Delta \tilde{p}_{ij} - \delta p_{ij}
\end{align}
$$
其中$\tilde{p}_{ij}$为预积分测量值，由IMU测量值和对bias的估计得到，$\delta p_{ij}$为其测量噪声

### 预积分测量噪声的协方差矩阵
$$
\begin{align}
A_{j-1} &= \begin{bmatrix}
\Delta \tilde{R}_{i,j-1} & 0 & 0 \\
 - \Delta \tilde{R}_{i,j-1}[\tilde{a}_{j-1} - b]\Delta t & I & 0\\
 - \cfrac{1}{2}\Delta \tilde{R}_{i,j-1}[\tilde{a} _{j-1}- b]\Delta t^2 & I\Delta t & 0\\
\end{bmatrix} \\
B &= \begin{bmatrix}
J_r^{j-1}\Delta t & 0 \\
0 & \Delta\tilde{R}_{i,j-1}\Delta t \\
0 & \cfrac{1}{2}\Delta\tilde{R}_{i,j-1}\Delta t^2 
\end{bmatrix} \\
\Sigma_{i,j} &= A_{j-1}\Sigma_{i,j-1}A_{j-1}^T + B_{j-1}\Sigma_\eta B_{j-1}^T
\end{align}
$$

### bias更新时的预积分测量值更新
定义：
- $\bar{b}$：旧bias
- $\hat{b}$：新bias
- $\delta b$：更新量

利用线性化来进行bias变化时刻的一阶近似更新方法

$$
\begin{align}
\Delta\hat{R}_{ij} &= \Delta\bar{R}_{ij}\exp(\cfrac{\partial\Delta \bar{R}_{ij}}{\partial \bar{b}}\delta b) \\
\Delta \hat{v}_{ij} &= \Delta\bar{v}_{ij} + \cfrac{\partial \Delta \bar{v}}{\partial \bar{b}}\delta b \\
\Delta \hat{p}_{ij} &= \Delta\bar{p}_{ij} + \cfrac{\partial \Delta \bar{p}}{\partial \bar{b}}\delta b \\
\end{align}
$$

### 残差定义
三项残差定义如下：
$$
\begin{align}
r_R &= \log\big[ (\Delta \hat{R})^T \Delta R \big] \\
r_v &= \Delta v - \Delta \hat{v} \\
r_p &= \Delta p - \Delta\hat{p}
\end{align}
$$

## 雅可比推导
$$
\begin{align}
P_b &= R_a^b P_a \\ 
v_b &= \dot{R}_a^b {R_a^b}^T P_b \\
\omega \times P_b &= \dot{R}_a^b {R_a^b}^T P_b  \to \\
   \dot{R}_a^b &=[\omega]R_a^b
\end{align}
$$
微小量：
$$
\begin{align}
R = R\exp(\delta \theta) = R (I + [\delta \theta])
\end{align}
$$
对于位置、角度、速度的微小量来说：
$$
\begin{align}
\delta\theta :\quad R_i &= R_i\exp(\delta\theta) \\
\delta P :\quad P_i &= P_i + R_i \delta P_i \\
\delta v:\quad v_i &= v_i + \delta v_i
\end{align}
$$
雅可比矩阵为：
$$
J = \lim_{\Delta x \to 0}\frac{F(x+\Delta x)}{\Delta x}
$$
### 速度增量
$$\Delta v_{ij} = R_i^t(v_j - v_i -g\Delta t_{ij})$$
雅可比：