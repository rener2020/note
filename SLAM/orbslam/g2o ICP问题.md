# 基于G2O的ICP匹配算法
2024-01-20

假设有两个点集$ps1$,$ps2$，其中的点关系：
$$p_1 = Tp_2$$
求解$T$

其匹配误差为：
$$
\begin{align}
\begin{bmatrix}
x_1\\y_1\\z_1
\end{bmatrix} &= T\begin{bmatrix}
x_\\y_2\\z_2
\end{bmatrix}
\end{align}
$$







