---
layout: post
title: Visual Concept综述（五）
description: >
  通过假定一个object part的先验，可以辅助dictionary的寻找，帮助模型的训练。
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述（五）

### 系列论文

#### [Interpretable and Accurate Fine-grained Recognition via Region Grouping]()

**motivation**:

分解原图像，找到对象的各个子部件（object parts），通过这些子部件综合判断。然而寻找object parts本就是个棘手的问题。作者关注在fine-grained recognition上，即同一个超类的图像部件有相同的视觉特征（如所有鸟的尾巴都有相同的形状）。

最主要的难点在于，只有image level标签的情况下，关于object parts的信息是不明确的。对此，作者创造性地提出一个先验假设：**对于一张图片，一个object part的出现是符合U形分布的**。例如基本每张鸟类图片都会出现鸟的头部，但不是所有图片都会出现鸟的腿部。

<!--more-->

**related work**:

与ProtoPNet的不同点：

1. 本文采用了region grouping方法来提供图像分段的解释
2. 本文模型的学习是由object part出现的一个强力前提控制的（即上面所说的先验假设）

**model**:

对于卷积层输出的feature maps $X_{1:N}=\{X_n\}$，与其对应实际类别标签$y_{1:N}=\{y_n\}$，其中$X_n\in \R^{D\times H\times W}$。子模型的目标即学习一个part dictionary$D=[d_1,d_2,\dots,d_K]\in \R^{D\times K}$(其中每个$d_k$代表一个object part concept，此即visual concept)以及决策函数$\hat{y}=\phi(X_n,D;\theta)$。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h49rjngurnj30r10av0zo.jpg" alt="image.png" style="zoom: 55%;" />

具体而言，该$\phi$函数由三部分组成：

1. Part Segmentation
2. Region Feature Extraction and Attribution
3. Attention Based Classification

由于唯一的监督标签是$y$，因此不能保证dictionary $D$一定能捕捉到有意义可解释的object parts。作者考虑了前面提到的prior，即object part $d_k$出现在set $X_{1:N}$中的概率$p(d_k\vert X_{1:N})$遵循U形分布。

**conclude**

本文最重要的贡献在于提出了一种更好地学习object part dictionary的方式，即通过假定一个object part的先验分布来控制训练过程。然而作者提出的U形分布在iNaturalist数据集上表现并不是特别好。如何找到更好的先验可以作为未来的研究方向。

