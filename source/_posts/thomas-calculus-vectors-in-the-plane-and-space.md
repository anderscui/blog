---
title: Thomas' Calculus - 平面与空间中的向量
date: 2017-09-13 01:37:09
tags:
	- Maths
---

# 平面向量和极坐标函数
	- Vectors in the Plane and Polar Functions

当一个物体在$xy$平面上漫游时，**参数方程**$y = f(t)$和$y = g(t)$可用来作为物体的运动和路径模型。这一章将了解**参数方程的向量方式**，用它来描述运动物体的轨迹，并计算其速度和加速度。

向量函数的一个主要作用是分析空间运动，行星运动最好用**极坐标**描述（由雅各布·伯努利首先发表），因为我们也将了解如何在这一新坐标系下分析曲线、导数和积分。

## 平面向量
	- Vectors in the Plane

我们测量时用到的量，分为两类，一是**标量**（Scalar），或称纯量，表示单纯的数值，如长度、质量与速率等；二是**向量**（Vector），或称矢量（因带有箭头而颇为形象），兼有大小与方向，如位移、速度与力等。

### 分量形式

向量用**有向线段**表示，箭头所向表示方向，长度指定大小。有向线段用$\overrightarrow{AB}$这样的形式表示，A、B分别为起点和终点；其长度用$|\overrightarrow{AB}|$表示。

如果两条有向线段长度相等且方向相同，则它们是**相等**的。更进一步，我们认为它们表示**同一向量**。（类似于，两个标量相等，则它们表示同一个量）

在笛卡尔坐标系中，两点确定出一条有向线段，从而它的长度与方向（即斜率）可以计算，因此可以确定两个向量是否相等。

与向量$v$相等的有向线段中，起点位于原点的那个是唯一的，称为$v$的**标准位置**。

定义：**向量的分量形式**

如果平面上的向量$v$的标准位置终点在$(v_1, v_2)$，那么$v$的分量形式是：

$$v = <v_1, v_2>$$

如此，平面上的向量与实数的**有序对**是一一对应的。这里$v_1$和$v_2$称为$v$的**分量**。向量$<v_1, v_2>$称为点$(v_1, v_2)$的**位置向量**。

若向量$v = <v_1, v_2>$用有向线段$\overrightarrow{PQ}$，P、Q坐标分别是$(x_1, y_1)$、$(x_2, y_2)$，则$v$的分量是：

$$v = <x_2 - x_1, y_2 - y_1>$$

向量$\overrightarrow{PQ}$的长度或大小为：

$$|v| = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$$

以上，都是中学数学解析几何的内容。

### 零向量与单位向量

**零向量**是$0 = <0, 0>$，长度为零，方向则不确定。

任何长度为1的向量$v$是**单位向量**。若$v$与正$x$轴夹角为$\theta$，则$v$的分量可表示为：

$$v = <\cos \theta, \sin \theta>$$

$\theta$从$0$变化到$2\pi$时，单位向量取遍所有可能的方向。

### 向量的代数运算

向量的两个主要运算是加法与数乘。

定义：**向量加法与数乘**

设$u = <u_1, u_2>, v = <v_1, v_2>$，$k$是实数

* 加法：$u + v = <u_1 + v_1, u_2 + v_2>$
* 数乘：$ku = <ku_1, ku_2>$

向量加法在几何上可使用**三角形法则**或**平行四边形法则**，一种非常直观的理解是物理中的**位移**概念。

至于数乘，若$k$为正，则$ku$的方向与$u$同；若$k$为负，则$ku$的方向与$u$反。另一方面，$|ku| = |k| \cdot |u|$。特别地，$(-1)u = -u$。

定义：**向量的差**

$$u - v = u + (-u) = <u_1 - v_1, u_1 - u_2>$$

注意到$(u - v) + v = u$，故向量加法与减法类似于数的加减法，减法可看作加法的逆运算。

### 标准单位向量

任何平面向量$v = <a, b>$都可以写成**标准单位向量**

$$i = <1, 0>, j = <0, 1>$$

的**线性组合**：$v = a<1, 0> + b<0, 1> = ai + bj$。$a$和$b$分别称为**水平分量/i分量**和**垂直分量/j分量**。

例：一个向量可以表示为与其同方向单位向量的数乘，求$v = 3i - 4j$的此种表示。

解：$|v| = 5$，那么$\frac{v}{|v|}$即为与其同方向的单位向量，令其乘以$|v|$即可得到原向量。结果是$v = 5(\frac{3}{5}i - \frac{4}{5}j)$。

$\frac{v}{|v|}$是与$v$同方向的单位向量，也称为$v$的**方向**。

### 切线和法线

一个向量是一条曲线在一个点$P$的**切向量**或**法向量**，如果它分别平行或垂直于曲线在该点的切线。切线斜率可由导数计算，而法线斜率则是导数的负倒数。

例：在曲线路径上，速度是切向量，如果物体受到法向量方向上的力，速度会发生变化。

## 点积
	- Dot Products

本节将了解如何通过向量分量计算两个向量的夹角，其关键是称为**点积**的表达式，点积亦称为**数量积**。之后还会介绍如何求解一个向量在另一个向量上的**投影**。

中学物理中，力的分解将一个向量分解为两个方向上的分量。特别地，如果要求力$F$在$v$的方向上的大小，改大小可以表示为$|F|\cos \theta$，这里的$\theta$是两向量的夹角。

### 向量夹角

如果两向量$u, v$起点重合，它们形成大小为$\theta$的角，这个角称为两者的**夹角**。

定理：**两向量的夹角**

两个**非零向量**$u = <u_1, u_2>$和$v = <v_1, v_2>$的夹角由如下公式给出：

$$\theta = cos^{-1} \frac{u_1 v_1 + u_2 v_2}{|u| |v|}$$

式中的分子部分称为向量的**点积**或**内积**，即各分量乘积之和，记为$u \cdot v$。故上述公式又可表示为：

$$\theta = cos^{-1} \frac{u \cdot v}{|u| |v|}$$

此定理可由三角形的**余弦定理**证明。该等式又可变形为：

$$u \cdot v = |u| |v| \cos \theta$$

### 点积法则

依实数性质，点积遵循若干运算法则，如交换律、分配律等，特别地有：$u \cdot u = |u|^2$

### 垂直（正交）向量

若两个非零向量$u, v$夹角为$\pi/2$，那么它们是**垂直的**或**正交的**。此时由夹角定理可知，$u \cdot v = 0$，反之亦成立。

由于零向量可以是任意方向的，故零向量垂直于任意向量。这样的规定也符合上段的结论。

### 向量投影

向量$u, v$起点重合，从$u$终点引垂线，由起点与垂足确定的向量称为$u$在$v$上的**投影**，记为$proj_vu$。其计算公式为：

$$proj_vu = (|u|\cos \theta ) \frac{v}{|v|} = (\frac{u \cdot v}{|v|^2}) v$$

其中$|u|\cos \theta$称为**$u$在$v$方向上的数值分量**。

### 把一个向量写成正交向量之和

由向量投影的定义，$v$与$u - proj_vu$是正交的，而且它们的和等于$u$，这样就得到了将$u$分解的方法：

$$u = proj_vu + (u - proj_vu)$$

### 扩展

柯西-施瓦茨不等式：$|u \cdot v| \leq |u| |v|$，当两向量平行时等号成立。

垂直于向量的直线：向量$v = ai + bj$垂直于直线$ax + by = c$。

平行于向量的直线：向量$v = ai + bj$平行于直线$bx - ay = c$。

## 向量值函数
	- Vector-Valued Functions

本节将以向量来研究平面上运动物体的路径、速度与加速度，其中有许多微分和积分概念从数值函数推广到了向量值函数。

### 平面曲线

当一个质点历经**时间区间**$I$在平面运动时，质点的坐标可看作定义在区间上的函数

$$x = f(t), y = g(t), t \in I$$

注：这又回到开始时接触到的**参数方程**。之所以选用参数方程而不是一般的$(x, y)$方程，是因为在诸如运动这类问题中，时间是更自然的自变量，而选择了时间作自变量，那么$(x, y)$也就自然地分开表示了。如果不用时间，那么何者为$x$，何者为$y$？它们的关系如何表示？

点$(x, y) = (f(t), g(t))$形成平面上的曲线，称为质点的**路径**。在时刻$t$的**位置**$P(f(t), g(t))$的向量

$$r(t) = \overrightarrow{OP} = <f(t), g(t)> = f(t) \text{i} + g(t)\text{j}$$

称为质点的**位置向量**。因此$f, g$称为位置向量的**分量函数**。于是质点的路径是经由时间区间$I$由$r$描绘的曲线。

$r(t)$定义在实变量上，函数值为向量，这就是所谓的**向量值函数**。相对地，一般实数值函数称为**标量函数**。

例：$r(t) = (t\cos t)i + (t\sin t)j, t \geq 0$表示一条螺线。

### 极限与连续

定义：**向量值函数极限**

若$r$的两分量函数在某点皆有极限，那么$r$也有极限：

$$\lim_{t \to c} r(t) = \lim_{t \to c} f(t)i + \lim_{t \to c} g(t)j$$

定义：**连续性**

若$\lim_{t \to c} r(t) = r(c)$，则$r$在$c$连续。

由极限定义可知，$r$在$c$连续，当且仅当$f$和$g$在$c$连续。

### 导数

定义：**向量值函数在一点的导数**

向量函数$r = fi + gj$在$t$有导数，若$f$和$g$在$t$有导数。导数是：

$$r'(t) = \frac{dr}{dt} = \frac{df}{dt}i + \frac{dg}{dt}j$$

注：目前可见，上述定义都是标量函数相应概念的自然推广。

若$r$在定义域内每个点都是可微的，那么说$r$是可微的。若$dr/dt$是连续的且从不为0（注意这里是零向量），那么说$r$描绘的曲线是光滑的（这意味着$f$和$g$有连续一阶导数且不同时为零）。

向量$dr/dt$存在且不为零时，$dr/dt$是曲线在该点的**切向量**，以此可求出曲线的**切线**。

向量值函数的导数是按分量计算的，而分量函数皆是一般的标量函数，因此向量值函数的导数继承了很多标量函数的性质，比较特别的是如下两个：

* 点积法则：$\frac{d}{dt}[u(t) \cdot v(t)] = u'(t) \cdot v(t) + v'(t) \cdot u(t)$
* 链式法则：$\frac{d}{dt}[u(f(t)] = f'(t) u'(f(t))$
* 标量乘积法则：$\frac{d}{dt}[f(t)u(t)] = f'(t)u(t) + f(t)u'(t)$

注意其中标量函数与向量值函数交错的地方。

### 向量函数在运动中的应用

在将质点的运动路径以向量表示后，速度、加速度等也随之确定下来了。

定义：**速度、速率、加速度、运动的方向**

若$r$是沿光滑平面曲线运动的质点的位置向量，则在时刻$t$，

1. $v(t) = \frac{dr}{dt}$是质点的**速度向量**，与曲线相切
2. $|v(t)|$是速度的大小，即速率
3. $a(t) = \frac{dv}{dt}$，速度的导数，或位置向量的二阶导数，称为**加速度向量**
4. $\frac{v}{|v|}$，一个单位向量，是**运动的方向**。

速度可以写成$|v|(\frac{v}{|v|})$，此即速率与方向之乘积。

至此可以看到，表示运动的函数向量化以后，相应的各个量都能容易地转为向量。

### 积分

定义：**不定积分**

$r$对$t$的不定积分是$r$的所有反导数的几何，用$\int r(t)dt$表示，若$R$是任一反导数，则有

$$\int r(t)dt = R(t) + C$$

定义：**定积分**

$$\int_{a}^{b} r(t)dt = (\int_{a}^{b} f(t)dt)i + (\int_{a}^{b} g(t)dt)j$$

可以说：积分之分量等于分量之积分。

注：微积分基本定理也适用于向量值函数（见习题9.3 43）：

* $$\frac{d}{dt}\int_{a}^{t}r(q)dq = r(t)$$
* $$\int_{a}^{b}r(t)dt = R(b) - R(a)$$

## 对抛射体运动建模
	- Modeling Projectile Motion

暂从略。

## 极坐标和图形
	- Polar Coordinates and Graphs

### 极坐标系

中学解析几何已经学习过极坐标系，它包含一个**极点**和一条**极轴**，这样平面上任一点都可以指定一个极坐标$(r, \theta)$。

$\theta$与三角学中的情形一样，有正负之分。同时一个给定点的角不是唯一的，因为不同的角可以重合；而且$r$也有正负之分，如果$r$为负，表示其方向与一般定义的方向相反。故$(2, 7\pi/6)$与$(-2, \pi/6)$表示同一个点。

### 极图形

* $r = a$，表示圆心在极点，半径为$|a|$的圆；
* $\theta = \alpha$，表示过极点的一条直线。

### 其他内容

暂从略。

# 空间中的向量和运动
	- Vectors and Motion in Space


