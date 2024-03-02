---
title: "计算机组成原理（2）：程序的计算机级表示"
date: 2024-02-21T16:46:39+08:00
lastmod: 2024-03-02T16:46:39+08:00
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
---


本文简单介绍**汇编语言 (assembly language)**. 源于作者水平有限，后续内容未能够完成，本文仅作简单概述。

## 汇编语言定义

CPU 计算时，负责按序执行输入的指令，这些指令都是二进制的，因此不可读。为了能够了解机器具体做的事情，汇编语言由此诞生。

**汇编语言是二进制指令的文本形式**，与二进制指令一一对应。[^1]

其文件名通常后缀为 `.asm`，生成方法见 [获取汇编代码](#获取汇编代码)

[^1]: [汇编语言入门教程](https://www.ruanyifeng.com/blog/2018/01/assembly-language-primer.html)


## 示例

![](assets/Pasted%20image%2020240302212558.png)

```cpp
int add(int a, int b) 
// pushq  %rbp 
// 将基指针寄存器（Base Pointer, `rbp`）的值压入栈中。这是函数调用的标准开始部分，用于保存调用者的基指针
// movq   %rsp, %rbp 
// 将栈指针寄存器（Stack Pointer, `rsp`）的值移动到基指针寄存器（`rbp`）中。这样做是为了设置当前函数的栈帧
    return a+b;
    // leal   (%rdi,%rsi), %eax
    // 将两个参数`%rdi`和`%rsi`相加的结果放入`eax`寄存器。`leal`指令通常用于地址计算，这里用它来执行加法操作。
    // popq   %rbp
    // 从栈中弹出之前保存的基指针寄存器（`rbp`）的值，恢复`rbp`寄存器的原值。这是在函数返回之前恢复调用者的栈帧。 
    // retq
	// 从函数返回。`retq`指令会从栈中弹出返回地址并跳转到那个地址执行，完成函数调用的返回过程。
}
// nopl   (%rax)

int main() {
// pushq %rbp
    return add(2, 3);
    // movq %rsp, %rbp
    // 将立即数`5`（即`add(2, 3)`的返回值）移动到`eax`寄存器中。在x86-64的调用约定中，函数的返回值通过`eax`/`rax`寄存器传递。
    // popq %rbp
    // retq  
}
```


## 访问信息

整数寄存器的表格：

![](assets/csapp02-整数寄存器.png)

### 操作数

操作数分成三类：
1. **立即数 (immediate)**，表示常数值，书写方式是 `$` 后面加常数值。例如：`$-5777`
2. **寄存器**
3. **内存引用**
### 数据传送

将数据从一个位置移动到另外一个位置的指令：**数据传送指令**——MOV 类.

```assembly
MOV   S, D               D<-S  传送
movq                           传送四字（64位）
movl                           传送双字（32位）
```

例子：
```assembly
movl $0x4050, %eax         Immediate--Register, 4 bytes
movw %bp, %sp              Register--Register, 2 bytes
movb (%rbi, %rcx), %al     Memory--Register, 1 byte
```

### 压入和弹出数据

标准的函数调用准备过程包括：

```assembly
pushq %rbp
movq %rsp, %rbp
```

1. 将基指针寄存器（Base Pointer, `rbp`）的值压入栈中。这是函数调用的标准开始部分，用于保存调用者的基指针
2. 将栈指针寄存器（Stack Pointer, `rsp`）的值移动到基指针寄存器（`rbp`）中。这样做是为了设置当前函数的栈帧

## 附录

### 获取汇编代码

假设已有名为 `test.c` 的文件，想将其转换成汇编语言

方法 1：
- 在 bash 中运行：
	```bash
	r # 字母 O 不是数字 0
	```
- 打开 `test.s` 文件。

方法 2：
- 在 bash 中运行：
	```bash
	gcc -c -Og test.c
	```
- 运行反汇编命令：
	```bash
	objdump -d test.o > test.asm # 通过管道导出
	```

## 参考资料

- [快速入门汇编语言](https://zhuanlan.zhihu.com/p/469950256)
