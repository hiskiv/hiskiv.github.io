---
layout: post
title: Visual Concept综述（二）
description: >
  VQ-VAE模型中离散的embedding space向量即是某种semantic representations。
tags: [专题笔记]
excerpt_separator: <!--more-->

---

## Visual Concept综述（二）

### 系列论文

#### [Neural Discrete Representation Learning](https://proceedings.neurips.cc/paper/2017/hash/7a98af17e63a0ac09ce2e96d03992fbc-Abstract.html)

##### Quesion

Discrete representations are a natural fit for complex reasoning, planning and predictive learning.

##### Model

VQ-VAE: combining the variational autoencoder (VAE) framework with discrete latent representations.

<!--more-->

其基本思想即将encoder的输出量化到离散的embedding space中的向量上。模型参数包括encoder、decoder中所有参数以及embedding space中的所有$e_k$。

<img src="http://tva1.sinaimg.cn/large/008qPTh8ly1h41mrpza74j312b0cagqc.jpg" alt="image.png" style="zoom:50%;" />

VQ-VAE模型结构如上图所示。

当图像输入x传入时：

1. 经过encoder，得到输出向量$Z_e(x)$，大小为$B\times D\times H\times W$。这里D是量化向量的维度。
2. 将$Z_e(X)$ reshape成大小为$B\times H\times W, D$，对每个D维向量，与embedding space中K个向量比较，找到距离最近的，向量$e_k$。
3. 将$e_k$作为VQ-VAE的编码。也为decoder的输入$z_q(x)$。

在学习时，我们直接将decoder输入$z_q(x)$的梯度向前传递作为encoder输出的梯度，如上图红色的$\nabla_z L$所示。（因为argmin是不光滑的）

我们可以将这里embedding space中学习得到的向量$e_k$视作从x分布中学习到的隐式表示，即visual concept。