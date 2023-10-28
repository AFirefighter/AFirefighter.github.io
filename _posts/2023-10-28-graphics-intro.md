---
title: 图形学初步
author: ygg
date: 2023-10-28 15:10:00 +0800
categories: [Blogging, Tutorial]
tags: [writing]
math: true
---

## 参数化曲面
参数化曲面（parametric surface）指的是用一个定义在二维流形上的连续函数f来刻画曲面。参数域就是二维流形，值域是$$ \mathbb{R}^3 $$的子空间。

$$
f(\mathbf{u})=\mathbf{p}\in \mathbb{R}^3;\mathbf{u}\in \mathcal{M}
$$

### 纹理坐标

## 体素表示
可以像表示二维图像那样表示三维图形。二维图像是一个个像素（pixel）的二维排列，三维图形
可以被理解为一个个体素（voxel）的三维排列。

## 隐式曲面
连续函数的零值点集可以隐式编码一个曲面。在使用marching cubes算法进行mesh重建的过程中使用过这一隐式表示。在三维空间中对连续函数f进行采样得到的有向距离场（signed distance field）是隐式曲面的离散表达。

$$
S=\{\mathbf{p}|f(\mathbf{p})=0\}
$$

## neural representation

