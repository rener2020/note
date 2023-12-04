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
B_{j-1}=\begin{bmatrix}
J_r^{j-1}\Delta{}t & 0 \\
0 & \Delta\widetilde{R}_{i,j-1}\Delta{}t \\
0 & \cfrac{1}{2}\Delta\widetilde{R}_{i,j-1}\Delta{}t^2 \\
\end{bmatrix}
\\
&
\eta_{ij} = A_{j-1}\eta_{i,j-1}+B_{j-1}\eta_{j-1}^a
\\
&
C_{ij} = A_{j-1}C_{i,j-1}A_{j-1}^T+B_{j-1}C_{\eta}B_{j-1}^T
\\
\end{align}
$$
bias更新时的预积分测量值更新使用一阶线性化近似更新（在这里体现防止重复计算累加）：
$$
\begin{align}
& \Delta{}R_{ij} \approx \Delta{}R_{ij}\exp(\frac{\partial\Delta{}R_{ij}}{\partial{}b^\omega}\delta{}b_{new}) \\
& \Delta{}v_{ij} \approx \Delta{}v_{ij} + \frac{\partial\Delta{}v_{ij}}{\delta{}b^\omega}\delta{}b_{new}^\omega + \frac{\partial\Delta{}v_{ij}}{\delta{}b^a}\delta{}b_{new}^a \\
& \Delta{}p_{ij} \approx \Delta{}p_{ij} + \frac{\partial\Delta{}p_{ij}}{\delta{}b^\omega}\delta{}b_{new}^\omega + \frac{\partial\Delta{}p_{ij}}{\delta{}b^a}\delta{}b_{new}^a
\end{align}
$$
其中：
$$
\begin{align}
& \frac{\partial\Delta{}R_{ij}}{\partial{}b^\omega}=\sum\limits_{k=i}^{j-1}(-\Delta{}R_{k+1,j}^TJ_r^k\Delta{}t) \quad J_r^k=J_r({}^b\omega-b)\Delta{}t \\
& \frac{\partial\Delta{}v_{ij}}{\partial{}b^\omega} = -\sum\limits_{k=i}^{j-1}(\Delta{}R_{ik}({}^ba_k-b_i^a)^\wedge\frac{\partial\Delta{}R_{ik}}{\partial{}b^\omega}\Delta{}t) \quad   \frac{\partial\Delta{}v_{ij}}{\partial{}b^a} = -\sum\limits_{k=i}^{j-1}(\Delta{}R_{ik}\Delta{}t)
\end{align}
$$

$$
\begin{aligned}
&\frac{\partial\Delta{\overline{\mathbf{P}}}_{ij}}{\partial{\overline{\mathbf{b}}}^{g}} =\sum_{k=i}^{j-1}\Biggl[\frac{\partial\Delta\overline{\mathbf{v}}_{ik}}{\partial\overline{\mathbf{b}}^{g}}\Delta t-\frac{1}{2}\Delta\overline{\mathbf{R}}_{ik}\cdot\left(\tilde{\mathbf{f}}_{k}-\overline{\mathbf{b}}_{i}^{a}\right)^{\wedge}\frac{\partial\Delta\overline{\mathbf{R}}_{ik}}{\partial\overline{\mathbf{b}}^{g}}\Delta t^{2}\Biggr]  \\
&\frac{\partial\Delta{\overline{\mathbf{P}}}_{ij}}{\partial{\overline{\mathbf{b}}}^{a}} =\sum_{k=i}^{j-1}\Biggl[\frac{\partial\Delta\overline{\mathbf{v}}_{ik}}{\partial\overline{\mathbf{b}}^{a}}\Delta t-\frac{1}{2}\Delta\overline{\mathbf{R}}_{ik}\Delta t^{2}\Biggr] 
\end{aligned}
$$
在估计中，全部的导航状态是$\mathbf{R}_i,\mathbf{p}_i,\mathbf{v}_i,\mathbf{R}_j,\mathbf{p}_j,\mathbf{v}_j,\mathbf{\delta}\mathbf{b}_i^g,\mathbf{\delta}\mathbf{b}_i^a$，

## IMU优化
三项残差如下：
$$
\begin{aligned}
\mathbf{r}_{\Delta\mathbf{R}_{ij}} &\triangleq\operatorname{Log}\left\{\left[\Delta\tilde{\mathbf{R}}_{ij}\left(\overline{\mathbf{b}}_{i}^{g}\right)\cdot\operatorname{Exp}\left(\frac{\partial\Delta\bar{\mathbf{R}}_{ij}}{\partial\overline{\mathbf{b}}^{g}}\delta\mathbf{b}_{i}^{g}\right)\right]^{T}\cdot\mathbf{R}_{i}^{T}\mathbf{R}_{j}\right\}  \\
& \triangleq \operatorname{Log}\left[\left(\Delta\hat{\mathbf{R}}_{ij}\right)^T\Delta\mathbf{R}_{ij}\right] \\
\mathbf{r}_{\Delta\mathbf{v}_{ij}} &\triangleq\mathbf{R}_i^T\left(\mathbf{v}_j-\mathbf{v}_i-\mathbf{g}\cdot\Delta t_{ij}\right)-\left[\Delta\tilde{\mathbf{v}}_{ij}\left(\mathbf{\overline{b}}_i^g,\mathbf{\overline{b}}_i^a\right)+\frac{\partial\Delta\overline{\mathbf{v}}_{ij}}{\partial\mathbf{\overline{b}}^g}\delta\mathbf{b}_i^g+\frac{\partial\Delta\overline{\mathbf{v}}_{ij}}{\partial\mathbf{\overline{b}}^a}\delta\mathbf{b}_i^a\right]  \\
&\triangleq\Delta\mathbf{v}_{ij}-\Delta\mathbf{\hat{v}}_{ij} \\
\mathbf{r}_{\Delta\mathbf{p}_{ij}}& \triangleq\mathbf{R}_i^T\left(\mathbf{p}_j-\mathbf{p}_i-\mathbf{v}_i\cdot\Delta t_{ij}-\frac12\mathbf{g}\cdot\Delta t_{ij}^2\right)-\left[\Delta\mathbf{\tilde{p}}_{ij}\left(\mathbf{\overline{b}}_i^g,\mathbf{\overline{b}}_i^a\right)+\frac{\partial\Delta\mathbf{\overline{p}}_{ij}}{\partial\mathbf{\overline{b}}^\mathrm{g}}\delta\mathbf{b}_i^g+\frac{\partial\Delta\mathbf{\overline{p}}_{ij}}{\partial\mathbf{\overline{b}}^a}\delta\mathbf{b}_i^a\right]  \\
&\triangleq\Delta\mathbf{p}_{ij}-\Delta\mathbf{\hat{p}}_{ij}
\end{aligned}
$$
雅可比求导如下：

$$
\begin{align}
&\frac{\partial\mathbf{r}_{\Delta\mathbf{R}_{ij}}}{\partial\delta\vec{\phi}_j}=\mathbf{J}_r^{-1}\left(\mathbf{r}_{\Delta\mathbf{R}_{ij}}\right) \\
&\frac{\partial\mathbf{r}_{\Delta\mathbf{R}_{ij}}}{\partial\widetilde{\partial\mathbf{b}_i^\mathrm{g}}}=\frac{\partial\mathbf{r}_{\Delta\mathbf{R}_{ij}}}{\partial\partial\mathbf{b}_i^\mathrm{g}}=-\mathbf{J}_r^{-1}\left(\mathbf{r}_{\Delta\mathbf{R}_{ij}}\right)\cdot\mathrm{Exp}\left(-\mathbf{r}_{\Delta\mathbf{R}_{ij}}\right)\cdot\mathbf{J}_r\left(\frac{\partial\Delta\mathbf{\overline{R}_{ij}}}{\partial\mathbf{\bar{b}^\mathrm{g}}}\partial\mathbf{b}_i^\mathrm{g}\right)\cdot\frac{\partial\Delta\mathbf{\overline{R}}_{ij}}{\partial\mathbf{\bar{b}^\mathrm{g}}} \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\widetilde{\partial\mathbf{b}_i^\mathrm{g}}}=\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\partial\mathbf{b}_i^\mathrm{g}}=-\frac{\partial\Delta\overline{\mathbf{v}}_{ij}}{\partial\mathbf{b}^\mathrm{g}},\quad\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\widetilde{\partial\mathbf{b}_i^\mathrm{a}}}=\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\partial\mathbf{b}_i^\mathrm{a}}=-\frac{\partial\Delta\overline{\mathbf{v}}_{ij}}{\partial\mathbf{b}^a}\\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\delta\mathbf{v}_i}=\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\mathbf{v}_i}=-\mathbf{R}_i^T \\
& \Large\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\delta\mathbf{v}_j}=\frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\mathbf{v}_j}=\mathbf{R}_i^T \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{v}_{ij}}}{\partial\delta\vec{\phi}_i}=\left[\mathbf{R}_i^T\cdot\left(\mathbf{v}_j-\mathbf{v}_i-\mathbf{g}\cdot\Delta t_{ij}\right)\right]^\wedge  \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\widetilde{\partial\mathbf{b}_{i}^{g}}}=\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\delta\mathbf{b}_{i}^{g}}=-\frac{\partial\Delta\mathbf{\overline{p}}_{ij}}{\partial\mathbf{b}^{g}},\quad\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\widetilde{\partial\mathbf{b}_{i}^{a}}}=\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\partial\mathbf{b}_{i}^{a}}=-\frac{\partial\Delta\mathbf{\overline{p}}_{ij}}{\partial\mathbf{b}^{a}} \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\delta\mathbf{p}_i}=\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\mathbf{p}_i}=-\mathbf{I} \\
&  \frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\delta\mathbf{p}_j}=\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\mathbf{p}_j}=\mathbf{R}_i^T\mathbf{R}_j \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\delta\mathbf{v}_i}=\frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\mathbf{v}_i}=-\mathbf{R}_i^T\Delta t_{ij} \\
& \frac{\partial\mathbf{r}_{\Delta\mathbf{p}_{ij}}}{\partial\delta\vec{\phi}_i}=\left[\mathbf{R}_i^T\cdot\left(\mathbf{p}_j-\mathbf{p}_i-\mathbf{v}_i\cdot\Delta t_{ij}-\frac12\mathbf{g}\cdot\Delta t_{ij}^2\right)\right]^{\wedge} \\
\end{align}
$$


