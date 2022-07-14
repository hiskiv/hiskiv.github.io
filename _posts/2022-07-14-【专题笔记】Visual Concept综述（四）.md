---
layout: post
title: Visual Concept综述（四）
description: >
  决策树：另一种高可解释性的模型。以及对visual concept系列概念的初步整理。
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述（四）

### 系列论文

#### [Neural Prototype Trees for Interpretable Fine-grained Image Recognition]()

**motivation/achievement**:

1. 希望在细粒度(fine-grained)图像分类任务上能取得更好的可解释性。决策树模型本身具有的强解释性与ProtoPNet相结合，可以得到更好的可解释性。
2. 在分类任务的准确度上比ProtoPNet更优，同时相比之下只需要10%的prototypes。

<!--more-->

**model**: Prototype tree (ProtoTree)

模型使用的决策树是soft decision tree，即在每个结点处，两个儿子均会向下经过，而不是仅选择其中之一。

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h46giyhmhvj30z009ldk1.jpg" alt="image.png" style="zoom:50%;" />

具体而言，输入图像$x$经过卷积网络得到输出$z=f(x;w)$，作为soft decision tree的输入。决策树上的每一个节点均是与ProtoPNet中所述相同的一个Prototype。在每个节点（设为$n$）处，其有两个儿子，分别为$n.left$代表原图中没有该prototype、$n.right$代表原图中存在该prototype。节点对应的prototype以sliding window的方式划过卷积层输出$z$的每个大小匹配的patch，找到L2距离最小的patch。通过之间的距离$d$，决策树预测“存在该prototype”的概率为$p_{e(n,n.right)}(z)=e^{-d}$。

而每个叶子节点均包括了一个各预测类别的分数，将其做softmax处理后与路径上的预测概率相乘即得到最终的预测分数。



### 关于visual concept的一个初步outline：

**Definition**: Properties of an image constituent. It can contain information of shape, material, high level concept or even (sub)object class.

**Form**: The form of visual concepts can be set of feature vectors (maybe discrete), or human specified pixel/region labels (eg. *Network dissection*).

**Origin**: Traditional model BoF, using codes from a discrete codebook to encode original image (without caring spacial relations).

**Motivation/goal**:

1. Can maintain a high interpretability (*ProtoPNet* etc. and *NetDissection* etc.)
2. Can be spanned to fit some discrete properties (*VQ-VAE* etc.)
3. Can ensemble some global properties/context of a category of images (*VQGAN* etc.)
4. Because the codebook is finite and discrete, it's easier to deal with

> (?) About network dissection based methods: seems against the grain here

**Some methods**:

- prototypes based (ProtoPNet, NP-ProtoPNet, Prototype trees)

- an embedding codebook of latent features (VQ-VAE, VQGAN) [similar to prototypes]

- dataset with human-labeled concepts on pixel level or region level (NetDissection)

  

- using heatmap and upsampling to visualize on the orignal image.

> 关于BagNet: 感觉严格上与这里的visual concept关系并不大，BagNet中的局部特征块是直接在原图上截取的，有点接近于Network Dissection一系。