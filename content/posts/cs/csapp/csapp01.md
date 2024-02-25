---
title: "计算机组成原理（一）：计算机性能"
date: 2024-02-21T00:49:03+08:00
lastmod: 2024-02-21T00:49:03+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- CS 
    - CSAPP
        - a?
tags: # 标签
- 计算机系统
description: ""
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
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

## 计算机性能指标
### 主频和时钟周期

**主频** $f$ 指 CPU 内核工作的时钟频率，与 **时钟周期** $\tau$ 互为倒数。

$$
f = \frac{1}{\tau}
$$

时钟周期是计算机中最小最基本的单位，一个时钟周期 CPU 仅完成一个最基本的动作。
### CPI

**CPI (clock cycles per instruction)** 是执行一条指令（平均）需要的时钟周期数。

其计算方式为：一段程序中所有指令的时钟周期数之和除以指令条数，或程序中各类指令的 CPI 乘以程序中该类指令的比例。

$$
\mathrm{CPI} \buildrel{\mathrm{def}}\over{=} \frac{1}{\sum_{k=1}^n I_{k}} \left( \sum_{k=1}^n n_{k}I_{k} \right)
$$
其中
- $I_{k}$ 是对于给定指令类型 $k$ 其指令数量；
- $n_{k}$ 是对于给定指令类型 $k$ 其时钟周期。

### MIPS

**MIP (million instructions per second)** 表示每秒钟执行指令的条数（以百万条，即 $10^6$ 为单位）

$$
\mathrm{MIPS} = \frac{1}{10^6T} \sum_{k=1}^n I_{k} \text{ where } T = \mathrm{CPI} \times \tau \times \sum_{k=1}^n I_{k}
$$
其中
- $T$ 是执行一百万条指令所消耗的时间
- $\mathrm{CPI}$ 是执行一条指令（平均）所需要的时钟周期数
- $\tau$ 是时钟周期，即执行一条指令所消耗的时间

因此也有

$$
\mathrm{MIPS} = \frac{f}{10^6 \mathrm{CPI}}
$$



【例子】使用 400 MHz 处理器执行具有以下指令组合和时钟周期计数的基准程序：

| Instruction Type | Instruction count | Clock cycle count |
| ---- | ---- | ---- |
| Integer Arithmetic | 45000 | 1 |
| Data Transfer | 32000 | 2 |
| Floating point  | 15000 | 2 |
| Control transfer | 8000 | 2 |

则该处理器 
$$
\mathrm{CPI} = \frac{45000\times 1 + 32000 \times 2 + \dots}{45000 + 32000 + \dots} = \frac{15 5000}{10 0000} = 1.55
$$
且
$$
\mathrm{MIPS} = \frac{4 \times 10^8}{1.55 \times 10^6} = 258
$$

### MFLOPS

**MFLOPS (million floating-point operations per second)** 是计算机每秒钟执行浮点操作的次数。

## Amdahl 定律

**Amdahl 定律描述了改善任何过程的一般原则**，其有关提升系统某一部分性能所带来的效果。其主要思想是：
- 当我们对系统的某个部分加速时，其对系统的整体性能的影响取决于该部分的重要性和加速程度；
- 要想显著加速整个系统，必须提升全系统中相当大部分的速度。

证明思路：假设优化前运行时间为 $T_{\mathrm{old}}$，对系统的某部分进行性能提升，其所需执行时间比例为 $0<\alpha <1$，性能提升比例为 $k$，即该部分现在所需时间仅有 $\alpha T_{\mathrm{old}}/k$.

新的执行时间为：

$$
T_{\mathrm{new}} = \frac{\alpha T_{\mathrm{old}}}{k}+(1- \alpha)T_{\mathrm{old}} = T_{\mathrm{old}}\left[ (1-\alpha)+\frac{\alpha}{k} \right]
$$

加速比为：

$$
S = \frac{T_{\mathrm{old}}}{T_{\mathrm{new}}} = \frac{1}{1-\alpha + \alpha / k}
$$
当 $k \to + \infty$ 时，$S \to 1 / (1-\alpha)$. 因此需要优化系统的大部分组件才能获得更大的提升。

## 参考资料

- [计算机组成原理笔记——计算机性能指标（CPI、IPS、MIPS等）](https://developer.aliyun.com/article/1194009)
