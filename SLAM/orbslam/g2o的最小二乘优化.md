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
## 可调参数化
