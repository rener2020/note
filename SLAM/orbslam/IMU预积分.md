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
&\hat{\omega}_b = \omega_b + b + \eta \\
&\hat{a}_b = R^b_w({}^wa - {}^wg)+b+\eta
\end{align}
$$
