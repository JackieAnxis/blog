---
title: "Kyrix-S: Authoring Scalable Scatterplot Visualizations
of Big Data"
tags: ["Visualization", "Scatterplot", "Authoring"]
date: 2021-04-20
author: PaperReading
mathjax: true
---

# 基本信息

- **论文：** Kyrix-S: Authoring Scalable Scatterplot Visualizations
- **作者：** Wenbo Tao, Xinli Hou, Adam Sah, Leilani Battle, Remco Chang and Michael Stonebraker
- **来源：** TVCG 2020

一作和他的导师（Michael）来自MIT的CSAIL可视化实验室。

Stonebraker的研究领域主要聚焦于数据库，特别是关系型数据库，曾获图灵奖。目前，Stonebraker是MIT的兼职教授。



# 复述文章

> 太长不看版：
>
> 作者做了一个大规模散点图的创作语法及其相应的系统，通过**配置语法**和一个**自动生成不同缩放层次布局**（mark的摆放位置）的方法，解决了大规模散点图的创作困难；

## Motivation&Challenges

- scatterplots能够提供一系列任务来揭示对于数据集的洞见。
- 以往的工作对于解决大数据集具有一些不足：
  - 基于聚类的工作无法inspecting individual objects；
  - 其他的工作使用了一系列不同scale的zoom level，但这些系统都具有scalability的问题。并且这些工作需要developer work来生成不同缩放层次上的标记放置位置。

## Contributions

- Kyrix-S：支持声明式创作的系统；
- 简洁且易于表达的声明式语法；
- 支持对大规模SSV (scalable scatterplot visualizations) 进行离线indexing以及交互式browsing的框架；

## 文章组织

1. 根据【existing guidelines and our experience with SSV users】定义了四个design goals，包括1. **快速创作**；2. **表达能力**；3. **可用的SSV** (在layout那边进行了详细介绍)；4. **scalability** (offline indexing + interactive online serving)；

2. 介绍了grammar的组成：

   - 先举了三个例子；
   - 其次讲了语法中的marks怎么绘制：基于template和自定义扩展；template是基于一些tasks精心挑选的，比如热力图、扇形图等等；
   - 最后讲了布局：对于X/Y/Z轴的配置选项；

3. 介绍了layout是怎么生成的。其实scatterplot本身没有是没有layout问题的，因为x/y方向都直接根据两个属性的值就能确定了；但因为涉及到重叠的问题，就需要把一些过度重叠的marks进行合并。于是作者系统的定义了layout这个问题：

   - **Non/partial overlap**: $\theta$参数来控制每个mark的bbox (bounding box)的允许重叠程度；
   - **Bounded visual density**: $K$参数控制viewport允许的mark数量上限；
   - **Zoom consistency**: 在i层的元素必须要在j层能被看到（j比i更深）
   - **Data abstraction quality**: 这一点感觉作者讲的有点模糊。首先说 cluster mark 的质量可以根据within-cluster variation (这个mark所代表的objects到mark的平均距离) 来进行刻画；后面又重复说了一次importance policy来说明abstract的时候需要保留重要的东西...

   然后作者开始介绍在单机（single node）上，如何处理layout的算法：

   1. 首先计算每个mark之间的ncd距离；如果$ncd \lt 1$，两个mark存在重叠；通过$\theta$来设置最小的ncd限制，$\theta$越小，mark之间越近，mark的density也会越高（因为一旦mark之间的$ncd<\theta$就会被合并起来）；

   2. 需要找到一个最小的$\theta$，使得在viewport中包含的mark数量不超过$K$；可以通过如下公式来估算当前viewport能够容纳的mark数量上限 ($\mathscr{P}(\theta)$)，只要设置$\mathscr{P}(\theta) \leq K$，就可以保证数量不超过K了；作者通过二分查找法进行查找。
      $$
      \mathscr{P}(\theta)=\left\lceil\frac{W_{V}}{W_{B} \cdot \theta}\right] \cdot\left[\frac{H_{V}}{H_{B} \cdot \theta}\right]
      $$

   3. 层次聚类：对于在$i+1$层的某个mark A，找到在$i$层，其ncd距离最小的mark B，如果$ncd_{A,B} \lt \theta$，就把B合并到A中。

   作者接下去描述了分布式的并行策略：通过KD树对于区域进行划分，不同区域用不同的计算节点进行处理，然后再处理在划分边界上的cluster；

4. 实验部分：展示了online serving的时间（数据获取的时间）以及offline indexing的时间；然后对比了Kyrix-S 和 他们之前的 Kyrix 的代码行数；

# 文章评价

个人比较赞赏这篇文章，因为它并没有用到非常难的技术，却解决了一个实际的问题。但说实话，因为其中不包含任何算法部分，novelty的确是一个比较大的问题，所有的设计都是很trivial的，但贵在其处理的数据量比较大，解决的问题比较实际，且代码是开源的。

- 值得学习的地方：
  1. introduction在老的问题（scaterplot authoring）定义了一个新的点（large scale），然后去描述前人工作的不足，可以学习一下套路；
  2. 一个grammar+一个类似algorithm的framework的组织形式，丰富了文章的contribution；做authoring相关的工作时，很容易遇到grammar本身没有什么新意，本身grammar就要让用户容易理解，这就导致它的设计本身，并不会有太多的新意（因为大家都能理解）；所以加入一些算法就会让工作不会显得那么trivial；

- 做的不足的地方：
  1. 对三个contribution的evaluation比较不足。不过创作型工具的确比较难进行实验对比；
  2. 其实文章并没有讲Kyrix-S这个系统，而是语法，把它作为contribution让人有点迷惑；

