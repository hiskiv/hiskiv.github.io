---
layout: post
title: Visual Concept综述：准备
description: >
  Visual concept的概念来源于计算机视觉的BOW模型。
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述：准备

### 从BoW model开始

词袋（Bag of words）模型是一种特征表示方式。最初用于文本特征上。最常见的词袋特征是词频，即一段文本对应词袋向量中的每一个分量代表该分量对应单词在文本中出现的次数。

BoW模型最主要的特点在于其并不包含原来文本中单词的顺序信息。

词袋向量显然不一定能很好的表示原来文本的特征，一是其无序性，二是某些stop words频率极高但并没有什么用。一种方法是通过对每个term进行weighting。

**n-gram**模型对无序性这一点做了一点改进。它将文本中连续的n个terms当做一个词，其余采用和BoW模型一致的处理方式。这样就保留了文本中的一些连续信息，而BoW模型则可以视作是n-gram模型中n取1时的特殊情况。

<!--more-->

### BoW model in Computer Vision

在计算机视觉中应用BoW模型，我们将一张图片看做一个文档，而将提取出的图像特征看做terms。在特征提取（往往使用SIFT特征）之后，每个图像即可以被视为一系列“词向量”（特征向量）的组合。最后，需要用这些词向量生成codebook，即所谓词典。一种方法是直接使用k均值聚类得到codebook，随后每个词向量便被分配到最近的code中，这样图像就可以使用直方图来表示。

视觉的BoW模型存在的一个问题是，通过k均值或其他一些方法得到的codebook没有可解释性。此外，它同样不保留图像中各个patch的空间信息。

### 系列论文

#### [This Looks Like That: Deep Learning for Interpretable Image Recognition](https://proceedings.neurips.cc/paper/2019/hash/adf7ee2dcf142b0e11888e72b43fcb75-Abstract.html)

##### question

能否让模型按一种像人类理解的方式，即将图像分解为一些原型部分，把这些部分与已有知识进行对比[this looks like that]来进行识别。

以往CNN的解释往往是通过1. posthoc visualization方式，这些方式并没有真正解释网络实际的推理过程是如何进行的。2. attention-based方式，然而该方式仅表现了网络关注哪些位置，而无法说明这些位置和哪些原型是相似的。

##### **model**：Prototypical part network (ProtoPNet)

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h40nnucryjj314q0gldnx.jpg" alt="image.png" style="zoom:40%;" />

ProtoPNet模型大致结构如下：

1. 卷积网络层（直接使用预训练好的ResNet、VGG、DenseNet等网络参数）
2. Prototype layer $g_P$
3. 全连接层

设卷积网络层的输出大小为$H\times W\times D$，学习到的m个prototypes为$\textbf{P} = \{P_j\}^{m}_{j=1}$，每个prototype的大小为$H_1\times W_1 \times D$，在文章对鸟类数据集分类里使用$H_1=W_1=1$。每个prototype可以视作某一类别的某一隐式特征，如上图中的$p_1$对应于某种鸟类的头部。

对于推理过程，输入图片$x$经过卷积层输出为$z=f(x)$，对第$j$个prototype，计算它与$z$中所有的patches（注意patches的大小与prototype大小是一致的）的squared $L^2$距离。这样我们能得到一个$H\times W$大小的activation map，对其进行上采样即可得到一个在原像素空间里的heap map，反映出了原图中哪个部分与学到的prototype最相近。

对这个activation map进行最大池化，得到一个相似度分数，在经过全连接层即可预测出原图像的类别。

##### 训练

每一轮经过三步训练：

1. Stochastic gradient descent (SGD) of layers before last layer

   学习到有意义的latent space，使得那些最重要的prototype patches能够聚集在真实类别prototype附近，而不同类别的clusters尽量分开。

   > 注意，哪些prototype对应哪一类、每一类对应几个prototype是我们事先规定好的超参数。

   在这一阶段，作者先训练卷积层与prototype层的参数，固定最后的全连接层参数，简单地将prototype连接到同一类的权重设为1，将prototype连接到其他类的权重设置为-0.5。

2.  prototypes投影

   这一阶段是为了便于在训练图像上可视化prototype。作者将prototypes投影到距离最近的同类样本patches，如下图所示。

   <img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h40p855vcuj30v501y401.jpg" alt="image.png" style="zoom:50%;" />

   作者证明，这一投影并不对原本的分类准确度造成多大影响。

3. 全连接层的凸优化

##### 实验

通过与ResNet、VGG、DenseNet等模型的对比，作者发现，使用了ProtoPNet之后并没有降低太多原有的准确率。另外，作者对比了几种可视化方式，发现ProtoPNet不仅能够highlights局部化注意力，还能够给出该部分“像”什么prototype。
