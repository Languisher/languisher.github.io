---
title: "博客快速部署"
description: "本文主要介绍如何快速部署一个像本站的个人博客。"
pubDate: "Aug 7 2024"
heroImage: "/post_img.webp"
tags: ["Blog"]
---

如何快速部署一个像本站的个人博客：
1. 前置要求：Xnix 机器，我个人用的 Macbook
2. 根据 [Astro 官网] 提供的教程 [Install and set up Astro](https://docs.astro.build/en/install-and-setup/) 使用 `npm run dev` 能够在本地运行网站
3. 我个人使用的模版：[Astrofy](https://github.com/manuelernestog/astrofy)，下载并替换所有文件
4. 创建 Github 仓库，保存代码，并且根据官方教程 [Deploy your Astro Site to GitHub Pages](https://docs.astro.build/en/guides/deploy/github/) 在 Github Pages 上部署
5. 搭建基于 Cloudflare 和 PicGo 的（免费）图床系统，具体参考这篇文章：[从零开始搭建你的免费图床系统 （Cloudflare R2 + WebP Cloud + PicGo）](https://sspai.com/post/90170)
6. Commit 你的工作！

可能遇到的问题：
- `npm` 网络问题：参考这篇文章修改代理 [npm查看源地址以及更换源地址](https://blog.csdn.net/weixin_37861326/article/details/107064092)
- Astrofy 模版 Workflow 无法正常运行：替换成官网  [Deploy your Astro Site to GitHub Pages](https://docs.astro.build/en/guides/deploy/github/) 中的代码