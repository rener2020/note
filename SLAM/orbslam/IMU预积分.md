# IMU预积分 
2023-11-07


在`Tacking`线程中，对预积分过程进行初始赋值。

``` c++
mpImuPreintegratedFromLastKF = new IMU::Preintegrated(IMU::Bias(),*mpImuCalib);
```

## 预积分
IMU测量模型如下：
$$
\begin{align}
&{}^b\hat{\omega} = {}^b\omega + b + \eta \\
&{}^b\hat{a} = R^b_w({}^wa - {}^wg)+b+\eta
\end{align}
$$
预积分模型：
$$
\begin{align}
& \Delta{}R_{ij}\triangleq \prod\limits_{k=i}^{j-1}\exp[({}^b\hat{\omega}-b-\eta)\Delta{}t] \\
& \Delta{}v_{ij} \triangleq \sum\limits_{k=i}^{j-1}\Delta{}R_{ik}({}^b\hat{a}-b-\eta)\Delta{}t \\
& \Delta{}p_{ij} \triangleq \sum\limits_{k=i}^{j-1} [ \Delta{}v_{ik}\Delta{}t + \frac{1}{2}({}^b\hat{a}-b-\eta)\Delta{}t^2] \\
\end{align}
$$
分离噪声得到测量量：
$$
\begin{align}
& \Delta{}R_{ij}\triangleq\Delta\widetilde{R}_{ij}\exp(-\delta{}\phi_{ij})=\prod\limits_{k=i}^{j-1}\exp[({}^b\hat{\omega}-b)\Delta{}t]\prod\limits_{k=1}^{j-1}\exp(-\Delta{}\widetilde{R}_{k+1,j}^TJ_r^k\eta_k\Delta{}t) \\
& \Delta{}v_{ij}\triangleq\Delta\widehat{v}_{ij}-\delta{}v_{ij}=\sum\limits_{k=i}^{j-1}[\Delta\widetilde{R}_{ik}({}^b\hat{a}-b)\Delta{}t]-\delta{}v_{ij} \\
& \Delta{}p_{ij}\triangleq\Delta{}\widetilde{p}_{ij}-\delta{}p_{ij}=\sum\limits_{k=i}^{j-1}[\Delta\widetilde{v}_{ik}\Delta{}t+\cfrac{1}{2}\Delta\widetilde{R}_{ik}({}^ba-b)\Delta{}t^2]-\delta{}p_{ij}
\end{align}
$$
预积分理想值为：
$$
\begin{align}
& \Delta{}R_{ij} \triangleq R_i^TR_j \quad \Delta{v}_{ij}\triangleq{}R_i^T(v_j-v_i-g\Delta{}t_{ij}) \quad \Delta{}p_{ij}\triangleq R_i^T(p_j-p_i-v_i\Delta{}t_{ij}-\dfrac{1}{2}g\Delta{}t_{ij}^2) \\
\end{align}
$$
有预积分测量值与理想值之间的关系：
$$
\begin{align}
\Delta{}\widetilde{R}_{ij} &\approx R_i^TR_j\exp(\delta\phi) \\
\Delta\widetilde{v}_{ij} &\approx R_i^T(v_j - v_i -g\Delta{}t)+\delta{}v_{ij} \\
\Delta{}\widetilde{p}_{ij}&\approx{}R_i^T(p_j-p_i-v_i\Delta{}t_{ij}-\cfrac{1}{2}g\Delta{}t_{ij}^2)+ \delta{}p_{ij}
\end{align}
$$
噪声协方差计算更新公式如下：
$$
\begin{align}
&
A_{j-1} = \begin{bmatrix}
\Delta\widetilde{R}_{j,j-1} & 0 & 0 \\
-\Delta\widetilde{R}_{i,j-1}({}^ba_{j-1}-b_i)^{\wedge}\Delta{}t & I & 0 \\
-\frac{1}{2}\Delta\widetilde{R}_{i,j-1}({}^ba_{j-1}-b_i)^{\wedge}\Delta{}t^2 & \Delta{}tI & I \\
\end{bmatrix}
\\
&
\\
&
\\
\end{align}
$$