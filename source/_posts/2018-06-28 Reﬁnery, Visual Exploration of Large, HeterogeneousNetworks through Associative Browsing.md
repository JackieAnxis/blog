---
title: Reﬁnery, Visual Exploration of Large, Heterogeneous Networks through Associative Browsing
date: 2018-06-28 15:16
author: PaperReading
tags: ["Visualization", "Graph Visualization", "Large Graph"]
mathjax: true
---

-   论文原文：Reﬁnery, Visual Exploration of Large, HeterogeneousNetworks through Associative Browsing
-   作者：S. Kairam, N. H. Riche, S. Drucker, R. Fernandez, and J. Heer
-   发表刊物/会议：2015 EuroVis

![image-20180628165322984](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2018-06-28-085323.png)

## 介绍

对数据进行探索有两大类策略：_analytical_（分析）和*browsing*（浏览）。前者是对特定的事实进行分析探索，后者则是适合信息搜寻任务。*HCI*的研究者们发现，_orienteering_（原意是定向运动）也是常见的探索策略，指的是向已知的信息目标进行导航。_associative browsing_（关联浏览）则表示想要导航的信息目标是特定的某个主题或者是一个广泛的知识领域。

本文的可视化系统称为*Refinery*，用以支持通过*associative browsing*关联浏览的方式进行大规模异构网络的探索。

**贡献**：

1. 确定了支持关联浏览的可视化系统的设计准则和策略
2. 制作了*Refinery*系统，通过关联浏览支持高效自底向上的异构网络探索
3. 一种新的基于随机游走的图算法，用以扩展异构网络的*DOI(degree-of-interest)*可视化

## 设计准则

通过回顾了一些之前的用以信息查询探索的可视化系统，作者提出了四个设计目标：

**G1**. 支持对异构动态集合的浏览。

**G2**. 在表达搜索意图时，平衡简洁性和表达性（表达性指的是提供给用户足够的选择，简洁性则是使得用户能够简单的来评估这些选择，以防产生选择障碍）

**G3**. 通过连续迭代的过程优化用户的搜索意图（交互式的对话窗口来帮助用户优化搜索意图）

**G4**. 提供多种上下文线索以支持用户识别数据，发现规律。

## 系统介绍

作者通过一个 case 来介绍了系统的各个面板。

Mae 最近参加了一个关于人机交互中的伦理学研究的会议，她想起会上有一个有趣的演讲，但是忘记了作者是谁。她只记得这篇文章赢得了 Honorable Mention。她想要找到这篇论文以及相关的论文。

##### Free-Text Search

Mae 在这里输入了关键词*ethics*，会返回一些相关的匹配结果（下图 a）。然后她继续输入了*Honorable Mention*。

![image-20180628162048090](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2018-06-28-082048.png)

##### Sidebar

*Sidebar*会列举所有的关键词，然后并提供 upvote 和 downvote 功能以便用户优化查询（上图 b）。Mae“upvote”了关键词*Design*，“downvote”了关键词*End of Life*和*E-Government*。

##### Graph View

![image-20180628163354240](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2018-06-28-083354.png)

在图视图中，与 Mae 选择的关键词相关的节点以及他们之间的连接被可视化出来。在图视图中，Mae 找到了一篇题为*Categorised Ethical Guidelines for Large Scale Mobile HCI*的文章，并阅读了摘要。她认为这篇文章已经十分接近她想要找的那篇文章，就将这篇文章添加到查询关键字中，这时，*CHI2013: Ethics in HCI*出现在图视图中，Mae 将这个节点也添加进查询中。

##### List View

![image-20180628163728991](https://jackie-image.oss-cn-hangzhou.aliyuncs.com/2018-06-28-083729.png)

切换到列表视图后，Mae 最终找到了她想要的那篇文章*Benevolent Deception in Human Computer Interaction*。

### 随机游走模型

作者将整个网络建模为一个基于随机游走的概率图模型，边表示从一个节点到另一个节点的概率。

$$
p(e_{i\rightarrow j})=\frac{w(e_{i\rightarrow j})}{p_H+(1-p_H)\sum_{e\in e_i}w(e)}
$$

$p_H$是在这个节点停止的概率。在只有一个查询节点时，计算这个节点到达其他节点的概率，并以这个概率作为相关系数。在多节点查询时，根据 upvote 和 downvote 的信息对每个查询节点产生的相关系数进行组合。
