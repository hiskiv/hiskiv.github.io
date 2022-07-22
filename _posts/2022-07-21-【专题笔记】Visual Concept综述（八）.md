---
layout: post
title: Visual Concept综述（八）
description: >
  占位符
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述（八）

### 系列论文

#### [Comprehensible Convolutional Neural Networks via Guided Concept Learning]()

Motivation:

作者观察到ProtoPNet系列工作得到的prototype有时不是很明确，比如prototype为图片背景、图片组成小部分或者一些object part的重叠部分，如下图所示。

因此作者希望能改造传统的CNN网络，希望每一个convolution filter得到的feature都能够对应到一个人类能正常理解的word phrases上。该方法（CCNN）通过在卷积网络中加上一层新的concept layer来引导visual concept与word phrases之间的近似。

Method:

Comprehensive CNN的Loss由三部分构成：Concept uniqueness loss、Mapping consistency loss以及Classification loss。

通过在backbone卷积网络的最后加上一层concept layer，用于提取concepts（作者这里的visual concept即concept layer中每个filter输出的结果）。这些concepts经过GAP层得到每个concept的激活值。

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220721183756532.png" alt="image-20220721183756532" style="zoom:50%;" />

- Concept uniqueness loss用以引导concept layer中得到的每个concept都唯一地对应一个word phrase。

- Mapping consistency loss用以尽可能降低concept和word phrase在一个embedding space（作者将concept向量与word phrase向量都投影到一个embedding space中来比较，在下面进一步阐述）中的距离。
- Classification loss即平常熟知的分类损失，而在GAP层后加入FC layer用来计算分类分数也能够较直接地起到可解释的作用。

具体而言，作者还提出一种采用counter image的方法来调整Mapping consistency loss的优化。所谓counter image，即对一个image的$kth$ concept而言，不包含该concept的图像。有了counter image，Mapping consistency loss便可由两部分组成，分别是semantic loss与counter loss。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h4eqvwnfawj30bv06fwgj.jpg" alt="image.png" style="zoom:70%;" />

如上图所示，首先，作者将input image和counter image的concept向量都投影到一个公共的embedding space中，以便进行相似度的比较。

semantic loss尽可能使某一个concept和对应词向量的相似度比该concept和其他词向量的相似度高，而counter loss尽可能使counter image的concept与其不包含的word phrase词向量尽可能远。

Experiment:

从下图中可以大致分析出新增的两个loss起到的作用。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h4er3lm45vj30gf09uago.jpg" alt="image.png" style="zoom: 67%;" />

Conclusion：

作者通过巧妙地对loss进行正则化，使image上可视化出的object part能具有非常明确的语义信息，并且能和word phrase对应起来。

#### [Interpretable Convolutional Neural Networks]()

Motivation:

在原有CNN模型的基础上，无需人为的多余标注以及多余训练（end-to-end），希望CNN中每个filter能对应到某个特定类别的某特特定object part。









