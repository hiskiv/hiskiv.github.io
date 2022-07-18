---
layout: post
title: Visual Concept综述（六）
description: >
  占位符
tags: [专题笔记]
excerpt_separator: <!--more-->
---

## Visual Concept综述（六）

### 系列论文

#### [Towards Robust Interpretability with Self-Explaining Neural Networks]()

**motivation**:

论文发表时间是2018年，这段时期大部分的可解释性工作还都局限在post-hoc的模式，这种解释方式往往是局部的（对每个sample单独解释），甚至有时产生矛盾的解释。因此作者试图提出一种self-explanable模型，让模型自身便带有可解释性（也是后面prototype等大部分模型的方向）。

此外，作者还提出了可解释模型需满足的三个性质：stability, explicitness, faithfulness，并论证以往可解释模型不可满足，但提出的模型可以满足这三条性质。

**model**:

作者给出了一类可解释性模型的定义：

$x\in \mathcal{X} \subset \R^n$与$\mathcal{Y}\subseteq \R^m$为模型输入与输出空间，则当模型$f:\mathcal{X}\rightarrow \mathcal{Y}$有形式$f(x)=g(\theta_1 (x)h_1(x),\dots,\theta_k(x)h_k(x))$时其为可解释预测模型。

其中:

1. $g$是单调的且是完全加法可分的 ($g(x+y)=g(x)+g(y)$)
2. 对每个$z_i:=\theta_i(x)h_i(x)$，$g$满足$\frac{\part g}{\part z_i}\ge 0$
3. $\theta$被$h$ locally difference bounded
4. $h_i(x)$是$x$的一个可解释表示
5. $k$较小

这一定义主要来源于可解释模型的鲁棒性，即输入稍作变化时输出不应当变化得过多。

这里的$h$可以理解为某种作为空间基得concepts，而$\theta$可理解为这些基的影响分数。

#### [Disentangling Visual Embeddings for Attributes and Objects]()

**motivation**:

以往的visual embedding space往往包含了图像的object feature与attribute feature。本文希望能将这二者解耦，即区分开来学习object与attribute的feature（如从削了皮的苹果中解耦出苹果[object]与被削皮特征[attribute]）。

这一方法能被应用在Compositional Zero-shot Learning上。另作者也提出将object与attribute进行组合能提高某些识别场景的表现。

**model**:

Object Attribute Disentaglement (OADis)











