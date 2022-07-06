---
layout: post
title: 【论文笔记】Batch Normalization
description: >
  Sergey等人提出的批归一化方法很好地平衡了初始化时的方差，并加快了训练速度，一定程度上也能够解决梯度消失问题。
tags: [论文笔记]
---

## 【论文笔记】Batch Normalization

论文原文链接：[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift](https://arxiv.org/abs/1502.03167)

这两天读了大名鼎鼎的ResNet，主要用于解决层数增加时的degradation问题。文章里提到了对于层数增加可能导致的梯度消失问题，可以通过Batch Normalization层解决。之前在cs231n里也有学习过BN层，但是整体学得懵懵懂懂，因此还是决定趁此机会好好把它弄明白。

> 一点题外话：前几天跟导师聊到如何读论文的问题。我感觉论文浩如烟海，每一篇都花两三天来细细地读是否太费时间了。导师的回答是，需要精度的论文主要就是那些最有名的、最有开创性的模型，其余论文大多是在这些模型的基础上加以改进，调整一下Loss或者调整一下Arch之类，因此精读完最有名的模型后，对于其他论文基本就可以快速理解。



