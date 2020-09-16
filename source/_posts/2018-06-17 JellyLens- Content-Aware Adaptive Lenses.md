---
title: JellyLens, Content-Aware Adaptive Lenses
date: 2018-06-17 17:29:00
author: PaperReading
tags: ["Visualization", "Fisheye", "Graph Visualization"]
mathjax: true
---

- 论文原文：JellyLens- Content-Aware Adaptive Lenses
- 作者：Cyprien Pindat, Emmanuel Pietriga, Olivier Chapuis, Claude Puech
- 发表刊物/会议：2012 TVCG

![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-18/18705982.jpg)

作者提出了 JellyLens，自适应的鱼眼透镜，根据内容来调整透镜形状，提高了焦点区域内容的可见性，又保留了较大部分的上下文区域。并进行了一个 user study 来证明其有效性。

## 整体思路：

1. 引出透镜，表达透镜的益处，然后说明现有透镜的缺点，总结本技术优势和本文贡献点
2. 介绍相关工作：围绕 2D 的鱼眼，3D 的，以及自适应的鱼眼透镜外加其他领域的类似工作
3. 方法概述：大概说明一下方法的思路
4. 详细描述
5. 评估（user study）
6. 讨论和未来工作

## INTRODUCTION

数据量的增加，了解这些数据需要多层透镜来进行展示，以便用户对数据进行不同层次粒度的查看。

三种技术：概览+细节，缩放和焦点上下文。其中最有名的焦点上下文就是鱼眼。

现有的鱼眼技术，存在以下缺点：透镜的形状是固定的，因此带来空间问题。

本文使用的 JellyLens，综合了 PathLens 和 AreaLens，前者能够根据焦点区域的特征进行自适应，后者则能够对焦点区与的物体进行重布局和重新缩放。

贡献是：1）对任意形状的透镜进行建模的方法；2）两种基于兴趣区域的几何形状动态生成相关透镜形状的方法（PathLens 和 AreaLens）

## RELATED WORK

分别介绍了：distortion-oriented、magic lens filter、3D 等技术，之后又介绍了一个带有自适应的领域相关的技术。

最后讲了一些图像领域的，content-aware 的工作。

## GENERAL APPROACH

JellyLens 能动态适应感兴趣区域，因此需要它能够采取任意形状的透镜。

分成三步来进行自适应：1. 获取场景中需要感兴趣的对象的几何形状；2. 根据在可视化中的位置以及其附近的感兴趣的区域来确定透镜的形状，其中 PathLens 适合轮廓和路径，AreaLens 适合填充区域；3. 通过透镜渲染区域。

## JELLYLENS MODELING AND RENDERING

本章主要描述如何根据已有形状来建模透镜。

将整个画面分成三个区域：上下文区域（context, $\cal C$），过渡区域（transition, $\cal T$）以及焦点区域（focus，$\cal F$）。

其中上下文区域，不存在扭曲；过渡区域的扭曲，则进行加权平衡；焦点区域的扭曲则用多个区域的组合来形成。

本章最后描述了实现算法：C++和 OpenGL

## ADAPTATION

分别描述了生成 PathLens 和 AreaLens 的形状的算法。

前者用来放大类似路径的对象（比如道路），效果类似于在蜘蛛网上的水滴。

后者则是用来放大一整块填充区域。

### PathLens

在该种透镜下，透镜形状主要受两个因素的影响：离鼠标的距离和离最近的对象的距离（指的是可以被放大的那些对象，比如地图中的高速公路等）。

文中用一个隐式函数来导出：当$f({\bf x}) = lens({\bf x - c}) + data({\bf x})=s$时，就是边界。大概效果如下：

![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-18/72742188.jpg)

### AreaLens

相比于前者只考虑最近的对象，AreaLens 考虑了所有感兴趣的对象，更适合用来密集区域的块状填充对象。

所有落在兴趣区域的对象都会有影响，而外面的则不影响。

提出了四个设计需求：

1. 放大的对象不能相互重叠
2. 对象的移动应该平滑
3. 拓扑信息应该更可能的保留
4. 上下文区域的对象应该既不被移动也不被放大

为了应对上述四个需求，分别用两种映射来进行：

1. dispersion mapping，用来完成位移，使得被放大的那些对象有足够的空间。
2. magnification mapping，用来完成放大离鼠标最近的那些对象（被称为放大对象，其余则被称为移动对象）

- **dispersion mapping**：1. 先对移动对象进行位移，位移向量会考虑所有方法对象，并进行加权；2. 缩小这些对象，保证不重叠
- **magnification mapping**：离鼠标越近的放大对象则被放的越大

![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-18/33950652.jpg)

## EVALUATION

目标是证明相对固定透镜所拥有的潜在的优点（potential benefits）和缺点（pitfalls）

比较了 AreaLens 和普通鱼眼（圆形，高斯核）

任务是找寻视觉元素

方法是：提出假设，证明不同因素的显著性

最后也做了一个问卷评价

## DISCUSSION AND FUTURE WORK

除了总结工作之外，作者提出了两个 future work：1. 更多的 evaluation；2. 自定义感兴趣的对象
