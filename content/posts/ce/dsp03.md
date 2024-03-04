---
title: "数字信号处理（3）：信号的采样与重建"
date: 2024-02-22T20:50:17+08:00
lastmod: 2024-03-04T20:50:17+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 数字信号处理
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

## 采样定理

对任意给定的模拟信号，我们应该如何选择采样周期 $T$ 或采样率 $F_{s}$,  使得我们能够从采样完成的数字信号无失真地重建原本的模拟信号？由此问题，我们提出了**采样定理**。

**采样定理 (sampling theorem)**：若想通过周期采样的方式，用采样后的数字信号 $x(nT)$ 准确地复原模拟信号 $x_{a}(t)$, 其中 $T$ 是采样周期，应当符合以下两个条件：
1. 模拟信号 $x(t)$ 应当是有限带宽的，即其最高频率 $f_{\max} < + \infty$.
2. 采样率 $f_{s}$ 应当符合条件：$f_{s} > 2 f_{\max}$.

采样定理的推导总结如下图。

![](Pasted%20image%2020240229012629.png)


