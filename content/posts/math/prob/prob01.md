---
title: "概率论与统计"
date: 2024-02-22T08:16:09+08:00
lastmod: 2024-02-22T08:16:09+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 概率论与统计
description: ""
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: false # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
math: mathjax
---


我们遵守的缩写：
- var = variable aléatoire réelle 实随机变量

## 分布函数

**分布函数 (Fonction de répartition d'une var)** 描述一个实随机变量 $X$ 的概率分布。对于所有实数值的随机变量 $X$，分布函数定义为：

$$
\boxed{\begin{align}
F_{X}: \mathbb{R} &\to [0, 1] \\
x &\mapsto \mathbb{P}(X \leq x) = \mathbb{P}(X^{-1}(]- \infty, x[)) 
\end{align}
}
$$

当我们有了分布函数时，其他所有事件都可以由分布函数得到，例如：
- $\mathbb{P}(Y>t) = 1 - \mathbb{P}(Y \leq t) = 1 - F_{Y}(t)$
- $\mathbb{P}(Y<t)=\lim_{ s \to t^- }\mathbb{P}(Y \leq s) \buildrel{\mathrm{Not}}\over{=} F_{Y}(t^-)$
- $\mathbb{P}(Y = t) = F_{Y}(t) - F_{Y}(t^-)$

更一般的，对随机变量 $Y = f(X)$ 我们有
$$
F_{Y}(t) = \mathbb{P}(Y \leq t) = \mathbb{P}(f(X) \leq t) = \mathbb{P}(X \in \mathcal{D}) = \int _{\mathcal{D}} f(t) \, \mathrm{d}t
$$

## 连续型随机变量

### 定义、密度函数

**连续型随机变量 (var absolument continue)** 定义：存在恒正、可测、在实数集上定义的函数 $f$ 使得
$$
\boxed{
\forall x \in \mathbb{R}, \; F_{X}(x) = \int _{- \infty}^x f(t) \, \mathrm{d} t
}
$$

我们将该函数命名为**密度函数 (densité de var)**. 证明一个函数是连续型随机变量的密度函数的方法，需要其满足两个条件：
- $f(x) \geq 0$
- $\int _{- \infty}^{+ \infty}f(t) \, \mathrm{d}t = 1$

对 $(X, f(x))$ ，其中 $f$ 是 varac $X$ 的密度函数（下略），有以下性质：

1. 分布函数 $F_{X}(x)$ 是连续的。
1. 对任意 $x_{1}<x_{2}$，有
	$$
	\mathbb{P}(x_{1}< x \leq x_{2}) = F_{X}(x_{2}) - F_{X}(x_{1}) = \int_{x_{1}}^{x_{2}} \, f(x) \,\mathrm{d}x 
	$$
	当 $\mathcal{B}$ 是实数的波莱尔测度空间时，$\mathbb{P}(X \in \mathcal{B}) = \int _\mathcal{B} f(x) \, \mathrm{d}x$
1. 在密度函数的连续点上，有
	$$
	f(x) = F_{X}'(x)
	$$
	证明：
	$$
	\begin{align}
	F_{X}'(x) = \lim_{ t \to 0 } \frac{f(x+ t)- f(t)}{t} &=\lim_{ t \to 0 } \frac{\int_{x}^{x+t} \, f(x')\mathrm{d}x' }{t}  \\
	&= \lim_{ t \to 0 } \frac{f(\xi).t}{t} \text{ where } \xi \in ]x, x+t[ \\
	&= f(x)
	\end{align}
	$$
