---
title: "计算机组成原理（5）：信息的表示和处理"
date: 2024-03-02T23:04:09+08:00
lastmod: 2024-03-02T23:04:09+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 计算机组成原理
description: ""
weight:
slug: ""
draft: true # 是否为草稿
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
math: katex
---



## 整数表示

### 无符号数编码

假设一个整数数据类型有 $w$ 位，位向量 $\vec{x} = [x_{w-1}, x_{w-2}, \dots, x_{0}]$ 表示向量的各个位数。

无符号数编码为：
$$
\text{B2U}_{w}(\vec{x}) = \sum_{i=0}^{w-1}x_{i}2^i
$$
易证每个介于 $0$ 至 $2^{w-1}$ 的数都对应有唯一编码。

### 补码编码

我们还希望表示负数值，最常见的是**补码 (two's-complement)**.

在这个定义中，编码的最高有效位被解释为负权 (negative weight)，其编码为：

$$
\text{B2T}_{w}(\vec{x}) = - x_{w-1}2^{w-1} + \sum_{i=0}^{w-2} x_{i}2^i
$$
在这种表示下，能够表示的最小值是 $\text{TMIN}_{w}=[10\dots 0]= -2^{w-1}$，而最大值是 $\text{TMAX}_{w} = [01\dots1] = 2^{w-1}-1$.

易证每个介于 $-2^{w-1}$ 至 $2^{w-1}-1$ 的数都对应有唯一编码。

### 原码、反码和编码

详情请见[原码, 反码, 补码 详解](https://www.cnblogs.com/zhangziqiu/archive/2011/03/30/ComputerCode.html).

二级结论：当 $n = [x_{w-1}, x_{w-2}, \dots, x_{t}, 1, 0, \dots, 0]_{2}$ 时，其负数的补码编码为 $-n = [\overline{x_{w-1}}, \overline{x_{w-2}}, \dots, \overline{x_{t}}, 1, 0, \dots, 0]_{2}$.



