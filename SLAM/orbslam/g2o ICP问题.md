# 基于G2O的ICP匹配算法
2024-01-20

假设有两个点集$ps1$,$ps2$，其中的点关系：
$$p_1 = Tp_2$$
求解$T$

其匹配误差为：
$$
\begin{align}
e &= p_1 - Tp_2 \\
&=\begin{bmatrix}
x_1\\y_1\\z_1
\end{bmatrix}-\exp(\xi)\begin{bmatrix}
x_2\\y_2\\z_2
\end{bmatrix}
\end{align}
$$
则有最优参数：
$$
\xi^{*} = \arg\min\sum||{}p_i^1 - \exp(\xi){}p_i^2||_2^2
$$
求解雅可比：
$$
\cfrac{\partial e}{\partial \xi} = \cfrac{\partial Tp_2}{ \partial \xi} = \begin{bmatrix}
I & -[Rp_2 + t] \\
0 & 0
\end{bmatrix}
$$







