---
layout: post
title: Visual Concept综述（七）
description: >
  占位符
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述（七）

### 系列论文

#### [Interpretability Beyond Feature Attribution: Quantitative Testing with Concept Activation Vectors (TCAV)]()

Key point：

如何表达一种可解释性？设ML模型的状态是一个由基向量$e_m$张成的向量空间$E_m$，其中$e_m$可以是如输入特征即神经元激活状态等。而人类大脑则在某些隐式向量$e_h$张成的空间$E_h$下工作。一个模型的“解释(interpretation)”即可视作一个函数$g: E_m\rightarrow E_h$。

具体而言，本文使用CAV (Concept activation vectors)来区分某一concept，而利用分类分数对CAV的方向导数来判断某一类别对某一concept的敏感度。

Model:

为了训练出某一个concept的CAV，首先需给出一些人工标注的图片数据，其中包含表示该concept的图片作为正样本，以及随机的不包含该concept的图片作为负样本。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h4den3km3gj30j209qq5e.jpg" alt="image.png" style="zoom:60%;" />

将这些图片送进要解释的网络中，对于其中间某层输出$f_l(x)$，训练一个线性二分类器，能够区分属于该concept的特征与不属于该concept的特征。最终训练得到的分类器方向$v_C^l\in R^m$即属于concept C，layer l的CAV，如上图所示。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h4derebi6nj30ao08xwfj.jpg" alt="image.png" style="zoom:60%;" />

随后，作者提出利用分类结果分数对CAV向量的方向导数可以作为该类对该concept的敏感度，称为Conceptual sensitivity。即$S_{C,k,l}(x)=\lim_{\epsilon\rightarrow 0}\frac{h_{l,k}(f_l(x)+\epsilon v_C^l)-h_{l,k}(f_l(x))}{\epsilon}$，为第k类object的分类结果对concept C的敏感度，如上图所示。作者将这种方法称作TCAV（Testing with CAVs）。

Drawbacks：

1. 需要收集大量的高维concept数据，太过依赖于人工标注。并且这些concept纯粹是一种标注，本质上仍为黑盒。
2. 只能逐样本地检查concept的影响度，无法得到包含整个dataset的人类可理解的concepts。

#### [Towards Automatic Concept-based Explanations]()

Motivation:

同样顺着TCAV的思路，考虑利用高维的concept来处理可解释性（相比于pixel level的解释），但也指出了TCAV的缺陷，即需要人工标注concept数据。因此本文提出ACE (Automated concept-based explanation)模型，能够自发地找到对ML模型分类起决定性作用、且对于人类来说是有意义的concept。

Desiderata:

可解释性visual concept的三个需要满足的性质：（作者指出这些性质可能并非visual concept需要满足的全部性质）

1. Meaningfulness
2. Coherency (同一concept的实例应相似，不同concept应有较大差别)
3. Importance

Methods:

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h4di34a04oj30q5094te3.jpg" alt="image.png" style="zoom:70%;" />

首先ACE在三个不同的粒度上对一类图像中的每个样本进行segmentation，这些segmentations将被作为concept的来源。随后，将这些segmentations送入CNN得到各自的特征，然后利用欧几里得距离对它们进行cluster，聚类结果即一系列的concepts。最后，ACE通过某种concept-importance score（文章中用的是TCAV分数）来得到这些concepts的重要性。

Limitations:

1. 这种方法仅适用于提取出以“一组像素”的形式而存在的concepts，仅通过segmentation提取concept无法学到更抽象级别的concepts
2. 依赖于segmentation算法的表现，而现阶段的大部分segmentation算法在多尺度上表现可能还不算太好





