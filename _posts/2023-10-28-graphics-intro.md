---
title: 图形学初步
author: ygg
date: 2023-10-28 15:10:00 +0800
categories: [Blogging, Tutorial]
tags: [writing]
math: true
---

# 曲面的表示

## 参数化曲面
参数化曲面（parametric surface）指的是用一个定义在二维流形上的连续函数f来刻画曲面。参数域就是二维流形，值域是$$ \mathbb{R}^3 $$的子空间。

$$
f(\mathbf{u})=\mathbf{p}\in \mathbb{R}^3;\mathbf{u}\in \mathcal{M}
$$

### 纹理坐标

## 体素表示
可以像表示二维图像那样表示三维图形。二维图像是一个个像素（pixel）的二维排列，三维图形可以被理解为一个个体素（voxel）的三维排列。

## 隐式曲面
连续函数的零值点集可以隐式编码一个曲面。在使用marching cubes算法进行mesh重建的过程中使用过这一隐式表示。在三维空间中对连续函数f进行采样得到的有向距离场（signed distance field）是隐式曲面的离散表达。

$$
S=\{\mathbf{p}|f(\mathbf{p})=0\}
$$

## neural representation

# 光栅化

所谓光栅化，就是指将三维空间中的物体绘制到二维的屏幕上。

**Transformation。**这个词指的是物体的移动变换，包括平移（translation），伸缩（scale）和旋转（rotation），不包括任何形变（deformation）。这三种变换可以用矩阵来表示，在3维情形下，一个3x3的矩阵能够表示旋转和伸缩的组合，但是不能表示平移。为了把平移统一到矩阵表示之中，需要使用齐次坐标，即增加一个维度，w，取值为1时表示点，取值为0时表示向量。一个w不为零的点$(x,y,z,w)$表示的点坐标为$(x/w,y/w,z/w)$。

**Rodrigues’ Rotation Formula**
以矢量$$\mathbf{n}$$为转轴，转过角度$$\alpha$$，其旋转矩阵
$$
\mathbf{R(\mathbf{n},\alpha)}=\cos(\alpha)\mathbf{I}+(1-\cos(\alpha))\mathbf{n}\mathbf{n}^T+\sin(\alpha) \left(\begin{array}{cc}0 &-n_z & n_y \\ n_z & 0 & -n_x \\ -n_y & n_x & 0 \end{array} \right)
$$

![calf](/assets/source/circle_cow.gif)

**Viewing （观测） transformation**

- Projection （投影） transformation
- Orthographic （正交） projection
- Perspective （透视） projection


## MVP Transformation
