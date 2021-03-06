---
layout: post
title: 【论文笔记】Batch Normalization
description: >
  Sergey等人提出的批归一化方法很好地平衡了初始化时的方差，并加快了训练速度，一定程度上也能够解决梯度消失问题。
tags: [论文笔记]
excerpt_separator: <!--more-->
---

## 【论文笔记】Batch Normalization

论文原文链接：[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift](https://arxiv.org/abs/1502.03167)

这两天读了大名鼎鼎的ResNet，主要用于解决层数增加时的degradation问题。文章里提到了对于层数增加可能导致的梯度消失问题，可以通过Batch Normalization层解决。之前在cs231n里也有学习过BN层，但是整体学得懵懵懂懂，因此还是决定趁此机会好好把它弄明白。

<!--more-->

### Introduction

在我们使用随机梯度下降方法（SGD）时，我们会往往面临这样的问题：每一层参数稍微的改变会导致后面整个网络的参数产生问题。也就是，网络每一层的参数需要不断去适应该层输入的分布。若输入的分布发生了一定变化，称作协变量偏移（Covariate shift）。如果能够保证每一层的输入分布维持不变，那么参数学习难度将大大降低。

我们可以将网络的一层稍稍扩展，考虑一个子网络。对于一个子网络，在使用Sigmoid激活函数时易发生梯度消失问题，输入的有些分量容易达到饱和域。我们可以使用ReLU激活函数一定程度上解决该问题；而BN提出了新的解决方案，若能保持在训练过程中非线性激活函数的输入分布稳定不变，我们将减少陷在饱和域的可能，从而加速训练。

> 对于网络内部节点之间产生的分布变化，我们称之为*internal covariate shift*。



### Towards Batch Normalization

#### 白化

早先的研究已经指出，对输入进行白化（whiten）操作能够使网络训练更快地收敛。所谓白化，即对输入进行线性变换，使输入分布的平均值为0，方差为单位值（1），且各变量间是不相关的。

利用白化的做法是：在神经网络的每一层输出，我们对其进行白化处理（即归一化），将结果的平均值与方差归一化后传入下一层。这种做法的问题在于，梯度下降过程忽略了正则化处理的影响，在训练过程中可能导致网络训练效果非常差。

#### 正则化层

如上面所讨论，我们需要一种正则化方式，使得其能够影响到梯度下降（或反向传播），或其能反映到梯度之中。我们可以将这种正则化视作一个“正则化层”，该层形如$\hat{x}=Norm(x,\mathcal{X})$，其中$x$是单个训练样本输入，而$\mathcal{X}$为整个训练集（或在批量SGD里理解为一个batch中）的所有样本。在反向传播计算梯度时，我们就得计算$\frac{\part Norm(x,\mathcal{X})}{\part x}$与$\frac{\part Norm(x,\mathcal{X})}{\part \mathcal{X}}$。若忽略后者，则导致了我们上面所说的情况，整个模型”blows up“。然而在实际中，要对白化层计算后一项是极其困难的，因此作者提出了Batch Normalization方法。

#### BN层

作者首先对原本的白化方法做了两个修改：

**修改1：**

首先，从原先对整个输入向量进行正则化转变为**对输入向量的每一个分量进行正则化**。设某一层的输入为d维向量$x=(x^{(1)}, x^{(2)},\dots, x^{(d)})$，我们对每一个分量进行正则化：$\hat{x}^{(k)}=\frac{x^{(k)}-E[x^{(k)}]}{\sqrt{Var[x^{(k)}]}}$。这样做的目的是易于反向传播时梯度的计算。

同时，我们注意到，直接对一层的每个输入进行正则化可能会改变该层能表示的语义内容。因此作者要确保，BN层要有能够表示恒等映射的能力。即，在必要的时候BN层能够不进行任何归一化操作，直接将输入信息传出。为了实现这一点，作者创造性地引入了两个参数：对每个分量$x^{(k)}$，设置参数$\gamma^{(k)}$与$\beta^{(k)}$来对分量进行缩放与平移。因此BN层最终的结果即$y^{(k)}=\gamma^{(k)}\hat{x}^{(k)}+\beta^{(k)}$。参数$\gamma^{(k)}$与$\beta^{(k)}$同样是通过梯度下降学习得到的。

可见，当$\gamma^{(k)}=\sqrt{Var[x^{(k)}]}$，而$\beta^{(k)}=E[x^{(k)}]$时BN层退化为恒等映射。

**修改2：**

由于我们通常采用mini-batch的SGD方法，因此我们在对分量进行正则化时，不再费力的对原问题的整个样本集分布求均值与方差，而用该batch中的样本来近似均值与方差。

这样我们得到了Batch Normalization Transform：$BN_{\gamma,\beta}: x_{1...m}\rightarrow y_{1...m}$，其forward pass的算法如下图所示：

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h3x76e1vzgj30mx0gj451.jpg" alt="image.png" style="zoom:50%;" />

注意，这里第2、3步中的平方与开根均为逐分量运算，与前面的修改1是一致的。

### 训练

对于上面的BN层变换，作者推导出了其反向传播的公式，如下图所示：

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h3x7lp8a1aj30lr0bzgqe.jpg" alt="image.png" style="zoom:50%;" />

训练及反向传播过程的算法较为复杂，此处不做详细介绍。

> 一个要点补充：训练时与推理时用的均值计算方法不尽相同，有精力了再看！



最后，对于卷积层，我们往往先固定通道，将通道这一维作为正则化的维度，对每个通道中所有位置，在整个批量中进行正则化（固定通道后的所有数据作为一个mini-batch，如下图蓝色部分所示）。对每个通道分别进行该操作，因此共需要与通道数相等数目的参数$\gamma$与$\beta$。

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h3x9ezfvlcj30dq0dx0wa.jpg" alt="image.png" style="zoom:50%;" />



### 总结

作者提出Batch Normalization主要是为了解决神经网络中各层输入可能出现的covariate shift问题。在引入了BN层之后，发现它能很有效地加快模型的收敛，同时还能有效解决梯度消失问题。

先前的白化层无法将正则化在梯度下降过程中体现出来，BN层对其进行了两处改进，即对各分量正则化以及在小批量中近似均值方差，前者方便反向传播时计算梯度，后者大大减少了运算量。当然，个人认为BN层最厉害的一点还是在于提出了分量的线性变换参数$\gamma$与$\beta$，这样BN层还可以一定程度上保持原有的数据表示。



> 延伸阅读：Group Normalization, Layer Normalization