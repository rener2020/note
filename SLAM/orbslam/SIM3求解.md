

# SIM3求解

2024-1-5

[转载自](https://note.youdao.com/ynoteshare/index.html?id=76a1d9452a3b6371464dd9b2745f5a2a)

本文内容为我在阅读论文Closed-form solution of absolute orientation using unit quaternions的笔记。  

单目SLAM在运行的过程中，不但会积累旋转和平移上的误差，还会产生尺度上的漂移。ORB-SLAM2中，对于单目的尺度漂移，回环优化的时候会做特殊的处理，即Sim3的优化。基本问题为一些已知的点是在两个不同的坐标系下测量的，目的是找到这两个坐标系的变换。和ICP不同的是,还需要多求一个尺度量
## 前期准备
设这两个坐标系中三个点的坐标分别为$r_{l,1},r_{l,2},r_{l,3}$和$r_{r,1},r_{r,2},r_{r,3}$,构造$x_l=r_{l,2}-r_{l,1}$,令： $$x^\wedge=x_l/\|x_l\|$$ 
上面是一个单位向量，令它的方向为左坐标系的x轴，现在设： $$y_l=(r_{l,3}-r_{l,1})-[(r_{l,3}-r{l,1})*x_l^\wedge]x_l^\wedge$$   
>上面式子$(r_{l,3}-r_{l,1})*x_l^\wedge$为向量相乘的数量积，大小为$(r_{l,3}-r_{l,1})$在x轴上的投影（没有方向），再乘个$x_l^\wedge$所以加上了方向：x轴正方向，整个式子得出的为$(r_{l,3}-r_{l,1})$在y轴上的映射，可以参考向量的加减（三角化定理）。

将$y_l$单位化得到Y轴：$y_l^\wedge=y_l/\|y_l\|$  
通过向量叉乘得到Z轴：$z_l^\wedge=x_l^\wedge \times y_l^\wedge$
右边的坐标系同理
现在得到基底$M_l=|x_l^\wedge y_l^\wedge z_l^\wedge|$和$M_r=|x_r^\wedge y_r^\wedge z_r^\wedge|$  
左边坐标系有一个向量$r_l$,那么：$M_l^Tr_l$是$r_l$在$M_l$基底下的坐标，左乘$M_r$变成右坐标系： $$r_r=M_rM_l^Tr_l$$ 变换为：$R=M_rM_l^T$  
设有n个点，在左右坐标系中分别表示为$\{r_{l,i}\}$ 和$\{r_{r,j}\}$,我们的目的是找到如下的变换形式： $$r_r=sR(r_l)+r_0$$ 
>这里s是一个比例因子，$r_0$是平移偏移量，$R()$表示旋转，且旋转保持尺度不变：$\|R(r_l)\|^2=\|r_l\|^2$

实际上肯定是存在误差，所以我们构造误差模型： $$e_i=r_{r,i}-sR(r_{l,i})-r_0$$ 
最小误差平方和模型： $$\sum^n_{i=1}\|e_i\|^2$$ 
下面我们先考虑总误差岁平移量的变化，再考虑尺度变化，最后考虑旋转。
## 计算平移量
计算质心：$r_l^-=\frac{1}{n}\sum^n_{i=1}r_{l,i}$ $r_r^-=\frac{1}{n}\sum^n_{i=1}r_{r,i}$  
将原点移到质心： $$r_{l,i}'=r_{l,i}-r^-_l$$ $$r_{r,i}'=r_{r,i}-r^-_r$$  $$\sum_{i=1}^n r_{l,i}'=0$$  $$\sum_{i=1}^nr_{r,i}'=0$$  $$r_{r,i}'=sR(r_{l,i}')-r_0'$$ $\Longrightarrow$ $$r_0'=r_0-r_r^-+sR(r_l^-)$$ 
优化函数变成：$\sum_{i=1}^n\|e_i\|^2=\sum_{i=1}^n\|r_{r,i}'-[sR(r_{r,i}')+r_0'\|^2]=\sum_{i=1}^n\|r_{r,i}'-sR(r_{l,i}')\|^2-2r_0'\cdot\sum_{i=1}^n[r_{r,i}'-sR(r_{l,i}')]+n\|r_0'\|^2$
最后一项明显不为负，所以当最后一项等于0时最小： $$min\sum_{t=0}^n\|e_i\|^2$$ $\Longrightarrow$ $$t=r_0=r_r^--sR(r_l^-)$$ 
## 尺度因子
上面推导得误差式为：$\|R(r_{l,i}')\|^2=\|r_{l,i}'\|^2$,因为上面旋转尺度不变性，并对误差式展开得 $$\sum_{i=1}^n\|r'_{r,i}\|^2-2s\sum_{i=1}^nr'_{r,i} \cdot R(r'_{l,i})+s^2\sum\|r'_{l,i}\|^2$$ 
将上式写成：$S_r-2sD+s^2S_l$  
进一步拆分成：$(s\sqrt{S_l}-D/\sqrt{S_l})^2+(S_rS_l-D^2)/S_l$   
上面的式子只有第一项与s有关，所以当第一项等于0时达到最小，即$s=D/S_l$: $$s=\sum_{i=1}^nr'_{r,i} \cdot R(r'_{l,i})/\sum_{i=1}^n\|r'_{l,i}\|^2$$
***
#### 改进方法
在误差模型中除一个根号s： $$e_i=\frac{1}{\sqrt{s}}r'_{r,i}-\sqrt{s}R(r'_{i,j})$$ 
>我们只是求误差的最小值，除以一个系数是没关系的，这样做的好处是最后求得的s与旋转因子无关，并且便于后面求解

经过类似的推导可得:$s=(\sum_{i=1}^n\|r'_{r,i}\|^2/\sum_{i=1}^n\|r'_{i=1}\|^2)^{1/2}$
## 尺度因子
之前的平移量和尺度因子两个部分都是通过把误差项=0时求解，当D尽可能大时，剩余的误差都最小化。所以一下求下面的部分最大化： $$\sum_{i=1}^n r'_{r,i} \cdot R(r'_{l,i})$$ 
用四元数表示：$\sum_{i=1}^n(q'r'_{l,i}q'^*) \cdot r'_{r,i}=\sum_{i=1}^n(q'r'_{l,i}) \cdot (r'_{r,i}q')$   
左边等于：$$q'r'_{l,i}=\left[\begin{matrix}
   0 & -x'_{l,i} & -y'_{l,i} & -z'_{l,i}\\
   x'_{l,i} & 0 & z'_{l,i} & -y'_{l,i} \\
   y'_{l,i} & -z'_{l,i} & 0 & x'_{l,i} \\
  z'_{l,i} & y'_{l,i} & -x'_{l,i} & 0
  \end{matrix}\right]=R_{l,i}q' $$  
右边等于：
$$r'_{r,i}q'=\left[\begin{matrix}
   0 & -x'_{r,i} & -y'_{r,i} & -z'_{r,i}\\
   x'_{r,i} & 0 & -z'_{r,i} & y'_{r,i} \\
   y'_{r,i} & z'_{r,i} & 0 & -x'_{r,i} \\
  z'_{r,i} & -y'_{r,i} & x'_{r,i} & 0
  \end{matrix}\right]= R_{r,i}q'$$  
  下面引入矩阵N： $$\sum_{i=1}^n(R_{l,i}q') \cdot (R_{r,i}q')=\sum_{i=1}^nq'^TR_{l,i}^TR_{r,i}q'=q'^TNq'$$ 
  下面求一个M矩阵： $$M=\sum_{i=1}^nr'_{l,i}r'^T{r,i}=\left[\begin{matrix}
S_{xx} & S_{xy} & S_{xz}\\
S_{yx} & S_{yy} & S_{yz} \\
   S_{zx} & S_{zy} & S_{zz}
  \end{matrix}\right]
  $$   
>这个M矩阵包含了解决旋转最小二乘问题所需的所有信息。可以通过M矩阵求解N矩阵。

N矩阵可与写成： $$N=\left[\begin{matrix}
(S_{xx}+S_{yy}+S_{zz}) & S_{yz} -S_{zy} & S_{zx}-S_{xz} & S_{xy}-S_{yx}\\
S_{yz}-S_{zy} & (S_{xx}-S_{yy}-S_{zz}) & S_{xy}+S_{yx} & S_{zx}+S_{xz}\\
   S_{zx}-S_{xz} & S_{xy}-S_{yx} & (-S_{xx}+S_{yy}-S_{zz}) & S_{yz}+S_{zy}   \\
   S_{xy} -S_{yx} & S_{zx}+S_{xz} & S_{yz}-S_{zy} &(-S_{xx}-S_{yy}+S_{zz}) 
  \end{matrix}\right]$$ 
**引出M来就是为了让其中的量来表示N，如上式。接下来就是求解N的最大的正特征值的过程。求出来的最大特征值，e对应其特征向量。并且就是四元数q的值。理解四元数是怎么表示旋转矩阵的然后就能求出旋转R，然后求出相应的平移t，尺度s也在前面的公式可以求出来。**
