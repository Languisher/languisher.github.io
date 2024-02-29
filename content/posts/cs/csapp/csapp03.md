---
title: "计算机组成原理（3）：算术逻辑单元"
date: 2024-02-26T21:36:35+08:00
lastmod: 2024-02-26T21:36:35+08:00
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

**算术逻辑单元 (arithmetic logic unit, ALU)** 是一种对二进制证书执行算术运算或运算的组合逻辑数字电路。[^1] 本文简单介绍 ALU 的实现。

---

## 电路符号[^2]

![](https://pic2.zhimg.com/80/v2-07bb42550ece5e83e24189f8e1608b35_1440w.webp)

## 算术逻辑单元的构建

### 一位半加器

考虑只有一位数的简易二进制加法电路，称之为**一位半加器**。其基本逻辑是：

![](一位半加器.jpeg)

由此绘图得：

![](Pasted%20image%2020240226200347.png)

### 一位全加器

之前一位半加器不考虑前一位的进位，现在我们考虑多位的加法。

![](一位全加器.001.jpeg)
![](一位全加器.002.jpeg)

$C_{i}$ 也可以这样考虑：
1. 首先考虑 $A_{i}$ 和 $B_{i}$ 是否进位。如果不进位且值为 1，则下一步（通过 XOR 门实现，筛去进位的可能性）。
2. 如果此时前一位有进位，则这一位同样也应该有进位。

因为我们也可以将 $C_{i}$ 写为：

$$
C_{i} = (A_{i} \wedge B_{i}) + (A_{i} \oplus B_{i}) \wedge C_{i-1}
$$

![](Pasted%20image%2020240226211110.png)


### 串行加法器




[^1]: [算术逻辑单元-维基百科](https://zh.wikipedia.org/wiki/算術邏輯單元)
[^2]: [D5：计算机组成原理-算术逻辑单元（ALU的功能和基本结构、一位全加器，串行加法器，并行加法器）](https://zhuanlan.zhihu.com/p/136165860)



