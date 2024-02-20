---
title: "Hugo 博客数学支持"
date: 2024-02-18T16:39:39+08:00
lastmod: 2024-02-18T16:39:39+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- 博客搭建
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

## 为 Hugo 博客添加数学支持的步骤

为 Hugo 博客添加数学支持的步骤：

1. 创建 `layouts/partials/helpers/katex.html` 文件，其内容是：

```html
<link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/katex@0.15.2/dist/katex.min.css"
    integrity="sha384-MlJdn/WNKDGXveldHDdyRP1R4CTHr3FeuDNfhsLPYrq2t0UBkUdK2jyTnXPEK1NQ"
    crossorigin="anonymous"
    referrerpolicy="no-referrer">

<script
    defer
    src="https://cdn.jsdelivr.net/npm/katex@0.15.2/dist/katex.min.js"
    integrity="sha384-VQ8d8WVFw0yHhCk5E8I86oOhv48xLpnDZx5T9GogA/Y84DcCKWXDmSDfn13bzFZY"
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
    type="text/javascript"></script>

<script
    defer
    src="https://cdn.jsdelivr.net/npm/katex@0.15.2/dist/contrib/auto-render.min.js"
    integrity="sha384-+XBljXPPiv+OzfbB3cVmLHf4hdUFHlWNZN5spNQ7rmHTXpd7WvJum6fIACpNNfIR"
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
    type="text/javascript"></script>

<script type="text/javascript">
  document.addEventListener("DOMContentLoaded", function() {
    renderMathInElement(document.body, {
      delimiters: [
        {left: "$$", right: "$$", display: true},
        {left: "\\[", right: "\\]", display: true},
        {left: "$", right: "$", display: false},
        {left: "\\(", right: "\\)", display: false},
      ],
    });
  });
</script>
```

2. 创建 `layouts/partials/helpers/mathjax.html` 文件，其内容是：

```html
<script type="text/javascript">
  MathJax = {
    tex: {
      displayMath: [['$$', '$$'], ['\\[', '\\]']],
      inlineMath: [['$', '$'], ['\\(', '\\)']],
    },
  };
</script>
<script
    async
    id="MathJax-script"
    src="https://cdn.jsdelivr.net/npm/mathjax@3.2.0/es5/tex-mml-chtml.js"
    integrity="sha384-+BSz3oj3ILMYvOBr16U9i0H4RZRmGyQQ+1q9eqr8T3skmAFrJk8GmgwgqlCZdNSo"
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
    type="text/javascript"></script>
```

3. 创建（如已创建则在末尾添加） `layouts/partials/extend_head.html` 文件，其内容是：

```html
{{ if (eq $.Page.Params.math "katex") }}
  {{ partial "helpers/katex.html" . }}
{{ else if (eq $.Page.Params.math "mathjax") }}
  {{ partial "helpers/mathjax.html" . }}
{{ end }}
```

4. 如果文章需要数学支持，可以在文章头部添加：`math: katex` 或 `math: mathjax`，示例：

```md 
---
title: "Writing math with Hugo"
tags: [hugo, math]
math: katex
---
```


## 参考链接

[Writing math with Hugo](https://misha.brukman.net/blog/2022/04/writing-math-with-hugo/)


