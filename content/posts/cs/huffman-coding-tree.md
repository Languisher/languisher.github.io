---
title: "Data Structure: Huffman Coding Tree"
date: 2024-05-02T21:07:35+08:00
lastmod: 2024-05-02T21:07:35+08:00
author: ["Languisher"]
keywords: 
- 数据结构
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 
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



## Introduction

In some cases, we need an improvement in space requirements in exchange for a penalty in running time.

To represent an item in a computer program, we often assign an unique code to each item or character, e.g. [ASCII](https://en.wikipedia.org/wiki/ASCII), which is a *fixed-length* coding scheme because each character has been coded with a fixed-length number.

At first glance, this seems fair, however it is based on the consumption that each character is used equally often. However, this is not the case. It is admitted that the letter E appears the most often, at least, much more often than the letter X.

Assigning them with shorter codes have the advantage that we can compress files into shorter ones. This technique is called **Huffman coding** by using **variable-length codes**.

## Background Information

You should be familiar with the basic concepts of **binary trees**.
## Building Huffman Coding Tree

The Huffman code for each letter is derived from a full binary tree called the Huffman coding tree. The following figures display an example of such case: 

![](Pasted%20image%2020240502203820.png)
![](Pasted%20image%2020240502203828.png)
In the tree,

- Each leaf of the Huffman tree corresponds to a letter.
- The weight of the leaf node is the weight (frequency) of its associated letter.
And the goal is to build a tree with the *minimum external path weight*. The **weighted path length** of a leaf equals to its weight times its depth.

To build such tree,
1. Create a collection of $n$ initial Huffman trees, now each of which is a single leaf node containing one of the letters. Put the $n$ partial trees onto a priority queue organized by the weight.
2. Remove the first two trees (with lowest weight), and join these two trees together to create a new tree, whose roots has the two trees as children, and whose weight is the *sum* of the weights of the two trees. Put the new tree back into the priority queue.
3. Repeat the previous step until all of the partial trees have been combined into one.

Below is an example to build up such tree:
![](Pasted%20image%2020240502204621.png)

## Proof of the effectiveness

Huffman tree building is a **greedy algorithm**, that is to say, it ensures that in each step, the algorithm is correct but it does not ensure that it give the desired result globally. We will proof this mathematically.

PROOF TO BE DONE

## Assigning and Using Huffman Codes

### Encoding

Beginning at the root, we assign either a 0 or 1 to each edge in the tree - 0 is assigned to edges connecting a node with its left hand side, and 1 its right hand side.

Below is an example of the process of encoding:
![](Pasted%20image%2020240502203828.png)
![](Pasted%20image%2020240502205251.png)

### Decoding

Decoding the message is done by looking at the bits from left to right *until a letter is decoded*. For example, given that `10110011101111101`, the first valid bit string with maximum length is `101` (`1011` does not exist), thus the first letter is `D`. You can verify that the word is `DUCK`.

There is no ambiguity for the code, and the set of codes meet the **prefix property** because no letter have the code with the same prefix. Otherwise, it should correspond to a an internal node of the tree, such as `111` is an internal node in the bit string `11111`, which is the letter `M`.

## Conclusion 

Huffman coding of a typical text file will save around 40% over ASCII coding, which displays its effectiveness.
