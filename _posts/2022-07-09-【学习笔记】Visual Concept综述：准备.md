---
layout: post
title: Visual Concept综述：准备
description: >
  DenseNet从ResNet受到启发，采用了一种“信息shortcut”的思路，创造性地将每一个block的输出连到后面所有block的输入上。DenseNet在保持与ResNet相近的准确率与误差的同时大大减少了参数的数量，被评为CVPR2017的Best paper。
tags: [论文笔记]
excerpt_separator: <!--more-->
---

## 【论文笔记】DenseNet

论文原文链接：[Densely Connected Convolutional Networks](https://openaccess.thecvf.com/content_cvpr_2017/html/Huang_Densely_Connected_Convolutional_CVPR_2017_paper.html)

这两天在读ResNet，赶紧趁热打铁读一读接下来2017年提出的DenseNet。DenseNet受了ResNet的很多启发，但本质其实与ResNet有不小的差异。并且，DenseNet在保持与ResNet相近的准确率与误差的同时大大减少了参数的数量，被评为CVPR2017的Best paper。

<!--more-->

### Problem

随着CNN网络层数逐渐的增加，人们发现存在degradation问题，同时网络前端的信息在传送过程中到最后很可能渐渐被磨损了。ResNet是采用了在每个残差块前后利用shortcuts连接，使得梯度能够直接流回；而FractalNetwork（没读过）则采用将数个并行层合并的方式。这些网络都在深度慢慢增大时取得了很好的效果，它们之间有一个共性，即使用shortcuts使前面层的信息能直接传递给后面的部分。

DenseNet则将这一特征发挥到了极致，它令每一层的输出feature maps连接到后面所有层的输入上，如下图所示。这与ResNet最不同的一点在于，ResNet本质上还是遵循传统架构的方式，每一层的输出是前面所有层的一个summation，而DenseNet直接将前面所有层的feature maps拼接在一起。

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h3y7dalwdej30nr0jwai3.jpg" alt="image.png" style="zoom:50%;" align=center/>



### DenseNet

#### 主体架构

设一个模型中，第$\mathcal{l}$层的非线性变换为$H_{l}(·)$，第$l$层的输入为$x_{l-1}$，输出为$x_{l}$。

传统的网络每一层的变换形如：$x_l = H_{l}(x_{l-1})$，

ResNet每一层（块）的变换形如：$x_l = H_{l}(x_{l-1})+x_{l-1}$，

而DenseNet则形如：$x_l=H_l([x_0,x_1, \dots,x_{l-1}])$。其中中括号括起的部分为张量拼接，具体而言，**这里的拼接是在通道的维度上进行的**。在实际中该$H_l(·)$变换往往由BN层、ReLU与卷积运算构成。一个Dense Block由数个这样的变换构成，而不同Dense Block之间通过transition layers连接，这些层由卷积与池化运算构成。

在一个Dense Block中，每一层输出的feature maps个数是一致的，作者称其为“增长率”(Growth Rate)。可以知道，第$l$层共有$k_0+k\times(l-1)$个feature maps输入，其中$k_0$为Dense Block输入层的通道数。

我们可以**把每层所产生的feature maps看做整个Dense Block的一个全局状态表示**，因为他们一旦产生，后面所有的层都能够访问到。因此Growth Rate即控制每层能对全局状态贡献的信息量。这一思想可以算作是DenseNet的核心。

#### Bottleneck层

为了提高运算效率，对于$H_l(·)$作者引进了bottleneck层，即1×1的卷积层。在每一层，输入有$k_0+k\times(l-1)$个feature maps，在本文中作者通过bottleneck层，将其约化为$4k$个feature maps，从而减少运算量。

#### Compression

作者为了提高模型的compactness（这个术语具体指什么？没有特别弄明白），对出于transition layer的卷积层filter深度进行限制。即对于输入transition layer的$m$通道feature maps，输出通道数控制到$\lfloor\theta m\rfloor$，其中$0\lt\theta \le1$为压缩比。

### 为什么DenseNet能减少参数数量

作者原文如下：

> DenseNet layers are very narrow (e.g., 12 filters per layer), adding only a small set of feature-maps to the “collective knowledge” of the network and keep the remaining featuremaps unchanged—and the final classifier makes a decision based on all feature-maps in the network

可见，最主要的提升在于DenseNet中每个卷积层的filter深度（即Growth rate）可以比较小。因为DenseNet每一层产生的所有feature maps均可视为整个模型的全局数据，这些数据全部参与了最终输出的决策。Growth rate小，因此参数总量也随之降低。

### 总结

就我个人读完的理解来说，DenseNet最核心的地方在于它充分利用了每一层得到的feature maps，将它们作为一种全局信息来使用，这对于较深的网络能够保持充分的信息。这一思想其实在ResNet中就已经得到利用，但ResNet的作者并没有从这个点进行思考，而DenseNet则是将这一点做到了极致。同时，因为是利用全局信息进行最终判断，因此每一卷积层需要的filter深度可以适当减少（为什么这样是优的其实还需要一些斟酌）。这样便达到了减少参数数量的目的。

这篇文章写得很详尽，作者的解释也很到位。后面的Training与Discussion部分感觉挺有意思，先挖个坑，后面有时间再看。

> 延伸阅读：显存友好的DenseNet：[Memory-efficient implementation of densenets](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1707.06990.pdf)
>
> DenseNet为什么是优的
