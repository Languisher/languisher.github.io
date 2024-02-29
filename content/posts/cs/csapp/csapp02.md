---
title: "计算机组成原理（二）：程序的计算机级表示"
date: 2024-02-21T16:46:39+08:00
lastmod: 2024-02-21T16:46:39+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 计算机系统
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
---

本文简单介绍**汇编语言 (assembly language)**.

## 汇编语言定义

## 获取汇编代码

假设已有名为 `test.c` 的文件

方法 1：
- 在 bash 中运行：
	```bash
	gcc -Og -S test.c # 字母 O 不是数字 0
	```
- 打开 `test.s` 文件。

方法 2：
- 在 bash 中运行：
	```bash
	gcc -c -Og test.c
	```
- 运行反汇编命令：
	```bash
	objdump -d test.o
	```

## 理解汇编语言

待补充，今天太累了。


## 参考资料

- [快速入门汇编语言](https://zhuanlan.zhihu.com/p/469950256)


