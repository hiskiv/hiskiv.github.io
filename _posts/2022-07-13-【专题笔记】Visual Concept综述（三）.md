---
layout: post
title: Visual Concept综述（三）
description: >
  对Visual Concept的进一步理解。
tags: [专题笔记]
excerpt_separator: <!--more-->

---

## Visual Concept综述（三）

### 系列论文

[Explaining Deep Convolutional Neural Networks via Latent Visual-Semantic Filter Attention]()

#### [APPROXIMATING CNNS WITH BAG-OF-LOCALFEATURES MODELS WORKS SURPRISINGLY WELL ON IMAGENET]()

Motivation: 仍然是出于原本深度神经网络缺乏可解释性的缺点而采用。考虑到在深度神经网络之前较常使用的BoF模型，希望能通过融入BoF模型特点来提高可解释性。

Model：BagNets - 融合了BoF模型与DNN，在保留BoF模型一定可解释性的同时拥有较好的表现。

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220713111722323.png" alt="image-20220713111722323" style="zoom:50%;" />

模型结构如下所示：

1. 首先在图像上截取多个$q\times q$大小的patch，都将其作为输入通过多个Residual Block得到一个2048维的特征表示。随后通过线性分类层得到1000维概率向量class evidence（假设分类结果有1000类），来决定判断每个patch的所属类别。（作者使用的Residual Block中卷积层大小均取为1×1，这样便将最上层的卷积层reception field限制到大小为$q\times q$）
2. 最终我们通过对每个patch类别的特征向量class evidence进行求平均，得到image level的class evidence
3. 最终使用softmax，找到image level中概率最大的类别，从而实现分类目的

在这里各个patch都有一个对应的class evidence向量，其直接在原图上进行提取，因此具有较强的可解释性。

##### 思考

1. BagNet是如何将BoF模型融入DNN的？

   BagNet模型中的BoF是离散选择的图像上的patches。这些原始的patches通过卷积层得到特征向量。而传统的BoF模型是先提取出特征，再对这些特征进行clustering。

2. 可解释性在哪里体现？

   由于原图中的每个patch都通过ResNet生成一个特征向量，这一特征向量包含了所有类别的概率，将各个类别的所有patch概率在heatmap上可视化便可以看出原图中各个类别被的判断情况。

#### [Network Dissection: Quantifying Interpretability of Deep Visual Representations]()