---
title: SchemeLens, A Content-Aware Vector-Based Fisheye Technique for Navigating Large Systems Diagrams
date: 2018-06-09 17:29:00
author: PaperReading
tags: ["Visualization", "Fisheye", "Graph Visualization", "Large Graph"]
mathjax: true
---

-   论文原文：SchemeLens: A Content-Aware Vector-Based Fisheye Technique for Navigating Large Systems Diagrams
-   作者：[Aurélie Cohé](https://dblp.uni-trier.de/pers/c/Coh=eacute=:Aur=eacute=lie)，[Bastien Liutkus](http://dblp.uni-trier.de/pers/hd/l/Liutkus:Bastien), [Gilles Bailly](https://dblp.uni-trier.de/pers/b/Bailly:Gilles) , [James Egan](https://dblp.uni-trier.de/pers/e/Egan:James)，[Eric Lecolinet](https://dblp.uni-trier.de/pers/l/Lecolinet:Eric)
-   发表刊物/会议：2016 TVCG

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-10/50334813.jpg)

## 整体套路介绍：

```flow
st=>start: Abstract
e=>end: Conclusion
op1=>operation: Introduction 背景技术，本文技术优点，贡献点
op2=>operation: Motivation 动机的来源，现有的缺点，设计目标
op3=>operation: Related Wrok 分模块介绍相关工作
op4=>operation: Techniques 详细介绍技术，技术的介绍都是由设计目标启发的
op5=>operation: User Study 两个实验，分别论证有效性和工作原理
op6=>operation: Discussion 围绕着实验和一些设计原则展开

st->op1->op2->op3->op4->op5->op6->e
```

## Introduction

第一段：介绍了焦点上下文技术，往往需要在空间和信息之间平衡（对局部增加空间，意味着其他地方的信息损失）

第二段：提出本文所使用的 SchemeLens，及其优点：向量化缩放（保持个体的形态，又不会增加空白空间）和考虑拓扑空间

第三段：提及简单的向量化缩放的缺点（重叠），简单介绍了解决问题的几个原则。

第四段：交互式的鱼眼技术的优点以及贡献

## Motivation

第一段：采访了工程师

第二段：提出一般的系统图纸所存在的缺点

第三段：提出了一些系统图纸所的特点

第四段：三个目标：感兴趣节点的 readability，最小化图纸结构的扭曲，在交互期间图纸结构的稳定性

第五段：详细描述了设计目标

## Related Work

分别描述了：鱼眼透镜，拓扑相关的鱼眼透镜，自适应透镜，导航技术以及感知&可读性的相关工作。

## SchemeLens 技术描述

![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-9/95973080.jpg)

Schemelens 综合了三个模块来做焦点上下文：

1. 放大透镜：

    靠近鼠标的聚焦区域，会被几何放大，给定节点的放大率和其到中心的距离负相关。

2. 拓扑透镜：

     在鼠标下面的元素会被放大，然后沿着该元素所在的路径进行放大，测地距离（geodesic distance，也就是拓扑距离）远的放大率低。该机制有利于调节相距比较远的节点。

3. 用户意图：移动鼠标时，只有沿着该路径并且和鼠标移动方向一致的节点会被放大，

### 焦距区域和可读性

#### 自适应的聚焦区域

聚焦区域是放大透镜的聚焦区域和拓扑透镜的聚焦区域的并集。

每个节点自身的放大，都是均匀的。

为了防止距离焦点近的节点放大后有重叠，但又不能让这些节点的位移过大，作者使用了弹簧模型。

**内容感知的缩放**：为了防止大的节点在放大之后变的太大，使用了一个反指数函数。

#### 聚焦区域的大小

放大效果所作用的节点应该被限制在一个合理数量以内。但这个『合理』受到很多因素的影响。

作者采用了限制『测地距离』的方式来限制节点数量。用户可以通过鼠标滚轮或者键盘来控制参数。

### 上下文区域和畸变

使用了三种启发式的目标来控制畸变：**空间稳定性**（spatial steability），**美学属性**（aesthetic properties），**时间稳定性**（temporal stability）

#### 美学规则

作者为整个图创建了一个层次结构：节点，包含这些节点的直线，包含这些直线的子路径。三条美学规则分别是：

1. 对齐。同一直线的节点要对齐（下图左），不同直线但在同一个局部环中的节点也要对齐（下图中），在同一直线上的交点也要对齐（下图右）。

    ![](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/18-6-9/69993075.jpg)

2. 正交。边之间的正交被保留。

3. 边交叉。不能引入新的边交叉。

#### 节点位移

因为放大效应不会放大空白区域，所以空白区域会被填充，反而能提高聚焦区域和上下文区域的连续性。

#### 空间和时间的连续性

如果没有适当的预防措施，焦点的略微变换就会引发节点位置或大小的巨大变动。针对位置和大小，作者采用了两种不同的策略：

1. 位置：弹簧模型能提供时间上的连续性，以平滑交互；
2. 大小：因为放大作用同时由两种不同的透镜同时产生（放大透镜和拓扑透镜），所以可能（1）即使用户用光标靠近它，节点的大小也可能减小，（2）节点的比例可能在某些边界处突然变化。

#### 捕捉用户意图

这一点是可选的。

当用户将光标沿着路径移动的时候，系统会将焦点沿着移动方向向前偏移一些。因此只有沿着路径的节点会被放大。当用户不沿着路径（光标的移动方向垂直于最近的路径或者光标远离所有路径）则不应用该行为。

### Implement

这一章节详细描述了各部分的实现（放大透镜、拓扑透镜，节点位移以及时空稳定性）

## User Study

第一段讲了两个 user study 的总览，包括目的，结果。

### 实验 1

实验一为了论证有效性

#### Experimental Protocol

1. 参与者和设备
2. 系统图纸的结构（也就是数据集）
3. 技术（需要对比的技术，SchemeLens 和传统的鱼眼）
4. 任务和过程（任务来源于实际工程，找到图纸中存在错误的节点）
5. 任务难度：用需要探索的路径数量来区分
6. 设计：主要描述了整个实验的参数：10 个参与者$\times$2 种技术$\times$2 个图纸$\times$3 种难度$\times$18 条测例

#### Results

1. 完成时间的分析
2. Subjective preferences：偏好投票

### 实验 2

实验二为了解释 SchemeLens 的自适应能力是如何影响用户表现的。

主要检验两个方面：拓扑自适应和用户意图自适应

将实验分成四组：

1. 没有用户意图自适应的鱼眼
2. 有用户意图自适应的鱼眼
3. 没有用户意图自适应的 SchemeLens
4. 有用户意图自适应的 SchemeLens

#### Experimental Protocol

类似于 Study 1

#### Results

1. 完成时间分析
2. Memorization：用一个五分 Likert 表来评估用户对图表的记忆
3. Subjective Preferences

## Discussion

整整一页的篇幅，围绕着以下的内容展开：

第一段：讨论 User Study 1 的结果，传统的鱼眼和本文技术

第二段：讨论拓扑自适应和用户意图自适应的意义

第三段：讨论了对提高记忆的帮助

第四段：可读性的讨论

第五段：空间、时间稳定性，也即结构不要发生太大变化

第六段：空白空间的意义

第七段：scalability

第八段：拓扑规则的影响，也就是之前提到的美学规则。

## Conclusion
