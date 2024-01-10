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
& R_j = R_i\prod\exp(\hat{\omega} - b - \eta)\Delta t \\
& v_j = v_i + g\Delta t + \sum R_k(\hat{a} -b -\eta)\Delta t \\
& p_j = p_i + \sum \big[ v\Delta t + \frac{1}{2} g\Delta t^2 + \sum \frac{1}{2} R(\hat{a} -b -\eta)\Delta t^2  \big] 
\end{cases} \to \\
& \begin{cases}
\Delta R_{ij} \dot{=} R_i^TR_j = \prod \exp \big( \hat{\omega} -b- \eta \big)\Delta t  \\
\Delta v_{ij} \dot{=} R_i^T(v_j - v_i - g\Delta t_{ij}) = \sum_{k=i}^{j-1}\Delta R_{ik}(\hat{a}_k - b_k - \eta_k)\Delta t \\
\Delta p_{ij} \hat{=} R_i^T(p_j - p_i -v_i\Delta t_{ij} - \frac{1}{2}g\Delta  t_{ij}^2) = \sum_{k=i}^{j-1}\big[ \Delta v_{ik}\Delta t + \cfrac{1}{2}\Delta R_{ik}(\hat{a} - b - \eta)\Delta t^2 \big]
\end{cases}
\end{align}
$$
三项残差定义如下：
$$
\begin{align}
r_R = \log
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