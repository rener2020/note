---
title: latex
category: 2022-10-13
renderNumberedHeading: true
grammar_cjkRuby: true
---
$O$

$P$

$p$

$xyz$


$$
Z\begin{bmatrix}
u\\v\\1
\end{bmatrix}=
\begin{bmatrix}
f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1 
\end{bmatrix}\begin{bmatrix}
X\\Y\\Z
\end{bmatrix}
$$


$$
\begin{cases}
x^{\prime} = x(1+k_1r^2+k_2r^4+k_3r^6)\\
y^{\prime} = y(1+k_1r^2+k_2r^4+k_3r^6)
\end{cases}
$$


$$
\begin{cases}
x^{\prime}=x+2p_1xy+p_2(r^2+2x^2)\\
y^{\prime}=y+2p_2xy+p_1(r^2+2y^2)
\end{cases}
$$


$p_1p_2$

$O_1O_2$


$e_1e_2l_1l_2$

$$p_2^TK^{-T}[t]RK^{-1}p_1=0$$

$p_2  \simeq K(R-\frac{tn^T}{d})K^{-1}p_1$




$$
\begin{cases}
 T = \begin{bmatrix}
R & t \\0 & 1
\end{bmatrix}  \\
T_W^O = T_W^C \cdot T_C^O \\

\end{cases} 
$$


$$T_C^O = \begin{bmatrix}
1 & 0 & 0 & 60 \\
0 & 1& 0 & 0 \\
0 & 0& 1 & 0 \\
0 & 0& 0 & 1 
\end{bmatrix}$$


老师 我按照您的意见调整了我的论文 具体调整如下
1. 调整了图格式，主要是将子图放在表格中并加上(a)(b)等序号和子图名称
2. 题目从“融合输电铁塔三维信息模型的机器人定位方法研究” 改为 “融合输电铁塔三维信息模型的机器人高空定位方法研究”
3. 添加了一节：
   2.3 机器人传感器配置和坐标系关系  这一节给出文章相关的机器人及其相机安装位置 并给出如何通过相机位置获取到机器人位置逻辑
4. 调整第三章章节顺序 调整后节顺序为 ：
   3.1 机器人局部定位与语义地标地图的构建 这一节给出初始机器人定位数据来源，来自相机结合IMU定位 
   3.3 融合铁塔信息模型的定位 这一节给出融合机器人初始定位数据和输电铁塔三维信息模型进行定位的方法

$$
\begin{cases}
R = \begin{bmatrix}
c\alpha c\beta & c\alpha s\beta s\gamma - s\alpha c \gamma & c\alpha s\beta c\gamma + s\alpha s\gamma \\
s\alpha c\beta & s\alpha s\beta s\gamma + c\alpha c\gamma & s\alpha s\beta c\gamma - c\alpha s\gamma \\
-s\beta & c\beta s\gamma & c\beta c\gamma
\end{bmatrix} = 
\begin{bmatrix}
r_{11} & r_{12} & r_{13} \\
r_{21} & r_{22} & r_{23} \\
r_{31} & r_{32} & r_{33} 
\end{bmatrix}
\\
t = \begin{bmatrix}
x \\ y \\ z
\end{bmatrix}
\end{cases}
$$



$$
\begin{cases}
\beta = {\rm Atan2}(-r_{31},\sqrt{r_{11}^2+r_{21}^2}) \\
\alpha = {\rm Atan2}({r_{21}}/{c\beta},{r_{11}}/{c\beta}) \\
\gamma = {\rm Atan2}({r_{32}}/{c\beta},{r_{33}}/{c\beta}) \\
\end{cases}
$$

$$
T = \begin{bmatrix}
R & t \\
0 & 1
\end{bmatrix}
$$


$$
T_W^O = T_W^TT_T^CT_C^O
$$

$$D(p) = \sum\limits_{i=1}^{n}2^{i-1}\varsigma(p;a_i;b_i)$$

$$
\begin{cases}
m_{pq}=\sum_{(x,y) \in PA}x^{p}y^{q}I(x,y) \\
\theta = \arctan m_{01} / m_{10}
\end{cases}
$$


$$
\begin{cases}
S=\begin{bmatrix}
x_1&x_2&x_3,\cdots \\
y_1&y_2&y_3,\cdots \\
\end{bmatrix}  \\
S_\theta = \begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta \\
\end{bmatrix}S
\end{cases}
$$


$$
H(D_1,D_2)= \sum\limits_{i=1}^{2}{\rm XOR}(D_1^i,D_2^i)
$$


$$
\begin{align}
s\begin{bmatrix}
u\\v\\1
\end{bmatrix}
&=K\begin{bmatrix}
R&|&t
\end{bmatrix}P = \begin{bmatrix}f_x&0&c_x\\0&f_y&c_y\\0&0&1\end{bmatrix}\begin{bmatrix}r_{11}&r_{12}&r_{13}&t_1\\r_{21}&r_{22}&r_{23}&t_2\\r_{31}&r_{32}&r_{33}&t_3\end{bmatrix}\begin{bmatrix}X_w\\Y_w\\Z_w\\1\end{bmatrix} \\
\end{align}
$$

$$x_t = f(x_{t-1},u_t,w_t)$$

$z_{t,j}=h(m_j,x_t,v_{t,j})$

$Q_0 = \cfrac{\sum\limits_{i=1}^{n} Q_i}{n}$

$$E(R,t)=\frac{1}{N}\sum\limits_{k=1}^{n}||x_{k1} - Ry_{k2}-t||^2$$

$$
\begin{cases}
u_x = \frac{1}{N}\sum\limits_{k=1}^{n}x_{k1} \\
u_y = \frac{1}{N}\sum\limits_{k=1}^{n}y_{k1} 
\end{cases}
$$


$$
\begin{cases}
x_{k1}^{\prime} = x_{k1} - u_{x} \\
y_{k2}^{\prime} = y_{k2} - u_{y}
\end{cases}
$$

构建M矩阵：
$$
M = \sum\limits_{k=1}^{n}x_{k1}^{\prime}y_{k2}^{\prime T}
$$

$$
M = U\Sigma V^T
$$

$$
\begin{cases}
R = VU^T \\
t  = u_x - Ru_y
\end{cases}
$$

$$y_i = Ry_i + t$$

$$
T_T^S =\begin{bmatrix}
R & t \\
0 & 1
\end{bmatrix}
$$

$$t_0 = \frac{\sum\limits_{i-1}^{n}y_i}{n} - \frac{\sum\limits_{i-1}^{n}x_i}{n}$$

$$
T_T^O = T_T^ST_S^O
$$

$$
c = \frac{m!}{(m-n)!}
$$

$\circ$
$$
\vec{ab}\times \vec{ac} \neq 0
$$

$$
\begin{cases}
0.75|\vec{ab}| < |\vec{e_id_i}| < 1.25|\vec{ab}| \\
0.75|\vec{cd}| < |\vec{d_ig_i}| < 1.25|\vec{cd}| \\
0.75|\vec{ad}| < |\vec{e_id_i}| < 1.25|\vec{ad}| 
\end{cases}
$$

$\epsilon = \frac{2\sum\limits_{k=i}^{n}e_k}{n}$

$e=|x_{k2} - Ry_{k1} - t|$


$$E(x)=\frac{1}{2}||Ra+t-b||^2$$

$x^* = \hat{x}+\Delta x^*$

$E(x)=\frac{1}{2}||f(x)||^2_2$

$T_S^O$

$$
T_T^S=\begin{bmatrix}
R & t \\
0 & 1
\end{bmatrix}
$$

$$
T_T^O = T_T^ST_S^O
$$

$A=\{a_1,a_2,\cdots,a_n\}$
$B=\{b_1,b_2,\cdots,b_m\}$


$$
\{(\gamma_2,\beta_2,\alpha_2),(\gamma_2,\beta_2,\alpha_2),\cdots\}
$$



$$
(\gamma_i,\beta_i,\alpha_i)
$$

$\{z_1,z_2,\cdots,z_g\}$

$\epsilon$

$$
\{a_1^{s},a_2^{s},\cdots,a_r^{s}\}
$$

$a^{s}_i$

$$
\{(x_1,y_1),(x_2,y_2),\cdots,(x_r,y_r)\}
$$

$5\circ$

$$\frac{|E_i - E_{i-1}|}{n} > \sigma$$

$$
T_S^C
$$


$$
T_C^O
$$


$$
T_a^c = T_a^cT_b^c
$$


$$
T = \begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix}
$$

$$
T_T^O = T_T^S T_S^C T_C^O
$$


$$
T_C^O = {T_O^C}^{-1} = \begin{bmatrix}
 0.        &  0.        &   1.        & -50.        \\
 0.90630779&  -0.42261826&   0.       & -42.54515595\\
 0.42261826&  0.90630779&  0.        & -50.73371352\\
 0.        &  0.       &   0.       &  1.        
\end{bmatrix}
$$


$$T_T^O = T_T^S T_S^O$$

$$
\begin{cases}
e_r = |\theta - \hat{\theta}|  \\
e_d = |d - \hat{d}|
\end{cases}
$$


$$T_C^O = \begin{bmatrix}
1 & 0 &  0 & -59.3\\
0 & 1 & 0 & -29\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}$$