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

**Motivation**: 仍然是出于原本深度神经网络缺乏可解释性的缺点而采用。考虑到在深度神经网络之前较常使用的BoF模型，希望能通过融入BoF模型特点来提高可解释性。

**Model**：BagNets - 融合了BoF模型与DNN，在保留BoF模型一定可解释性的同时拥有较好的表现。

<!--more-->

<img src="http://tva1.sinaimg.cn/large/008u1Mkqly1h45gppj693j30kk0g0ju7.jpg" alt="image-20220713111722323.png" style="zoom:50%;" />

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

**motivation**:关注可解释性。disentangled representation：解耦表征，将特征表示分成数个互相独立的因素，且每个因素都能表示某种语义含义。

**Broden数据集**：作者搭建了一个数据集Broden，其中有pixel-wide的各种语义标注（包括颜色、材料、物体、纹理等），有点像segmentation。

**Method**: Network Dissection

1. 整理人为标记好的visual concept集合
2. 确定各个隐藏单元对各个concept的响应程度
3. 量化隐藏单元重合情况（concept pairs，IoU）

对于一个训练好的网络，将Broden数据集中的图片输入，对中间第k个hidden layer的输出，对于第c个concept，进行binary segmentation，并上采样到原图大小。随后通过计算该结果与原语义标注的IoU得到interpretability的分数。该分数也可以视作第k个hidden layer对于concept c的准确性。

**思考**：这里的visual concept是人为标注、设定的，缺乏灵活性。可能存在某些隐式的特征人类一般不会考虑到。

这里的visual concept与ProtoPNet与VQ-VAE中的那种似乎并不是同一个东西qwq



#### [Taming Transformers for High-Resolution Image Synthesis]()

:star:**CVPR 2021 Oral**

**Buzzling point**: 结合CNN与Transformer各自的优点，在高分辨率图像合成上取得了优秀的结果。我们现在主要关心的是，这篇文章中首先通过CNN学习到一个包含上下文内容的图像组成vocabulary，再通过这些vocabulary的复合通过Transformer拟出高分辨率图像。

**Method Motivation**: 高分辨率图像合成需要理解图像的全局复合，使其在局部真实的同时也要保证全局的pattern是一致的。因此作者认为，比起在pixel粒度表示图片，更应该使用一种上下文丰富的code来表示图片。

**Model**: （CNN部分）

一个codebook：$\mathcal{Z}=\{z_k\}^{K}_{k=1}\subset \R^{n_z}$, 其中$n_z$为codes向量的长度，$z_k\in\R^{n_z}$。每个图片可以通过codes的空间性排列构成，即$z_q\in \R^{h\times w\times n_z}$。

为学习codebooks，作者构建一个由Encoder(E)与Decoder构成的卷积模型，二者结合即能学习如何通过离散的codebook元素表示一个图像。具体而言，对于图像$x$，通过该模型进行表示得$\hat{x}=G(z_q)$，其中$z_q=\textbf{q}(\hat{z}):=(argmin_{z_k\in \mathcal{Z}}\|\hat{z}_{ij}-z_k\|)\in \R^{h\times w\times n_z}$，$\hat{z}=E(x)$。注意，$\hat{z}_{ij}$的大小是$n_z$。

作者改造了VQ-VAE模型，提出VQGAN模型，来最大化地压缩codebook。