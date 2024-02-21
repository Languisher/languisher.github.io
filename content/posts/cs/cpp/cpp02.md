---
title: "C++ 基础（二）：函数"
date: 2024-02-21T16:49:48+08:00
lastmod: 2024-02-21T23:41:48+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- C++
description: ""
weight: 2
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

## 函数传递

每次调用函数时都会
1. 重新创建它的形参
2. 用传入的实参对形参进行初始化

**函数传递原则：形参初始化的机理与变量初始化一致。** 如果形参是 *引用* 类型，它将 *绑定* 到对应的实参上；否则，将实参的值 *拷贝后赋给* 形参。前者称为 **传引用调用 (called by reference)**，后者称为 **传值调用 (called by value)**.

**使用引用避免拷贝。** 拷贝大的类类型对象或者容器对象非常低效，有的类甚至不支持拷贝操作。此时，函数只能通过引用形参访问该类型对象。

### 传值调用

函数对形参的所有操作都不会影响实参，以函数指针为例：

```cpp
void reset(int *ip)
{
	*ip = 0; // 改变 ip 所指对象的值。
	ip = 0; // 只改变 ip 的局部拷贝，实参没有发生变化。
}

int i = 42;
reset(&i); // 改变 i 的值而非 i 的地址。
```

### 传引用调用

通过使用引用形参，对于形参的操作实际上是作用在引用所引的对象。

```cpp
void reset(int &i) { i = 0; }

int j = 42;
reset(j); // j 值被改变。
```

函数返回值只能有 1 个，但我们可以通过引用形参返回额外信息。

```cpp
string::size_type find_char(const string &s,
						     string::size_type &occurs)
{
	auto ret = s.size();
	occurs = func();
	return ret; // occur 通过引用隐式地返回了
}
```

### const 形参和实参

> 注：如果本部分觉得困难，请回顾 C++ 基础（一）中关于 const 限定符的讨论。

规则：
1. 当实参初始化形参时会忽略形参的顶层 const.
2. 可以使用非常量初始化一个底层 const 对象，但不能用常量初始化一个非底层 const 对象。
3. 由 2 可得，**当形参不会改变时，尽量使用常值引用**，以避免限制函数所能接受的实参类型。

常见错误：
```cpp
// 1
void fcn(const int i) { /* 不改变 i 的操作 */ }
void fcn(int i) { /* ... */ } // 错误：重复定义，与上面函数完全一致，底层 const 被忽略
// 2
const int ci = i;
void reset(int &); // 函数声明
reset(ci); // 错误：不能用常量初始化一个非底层 const 对象。
```

### 含有可变参数的函数

## main 函数

`main` 函数支持两个可选参数：

```cpp
int main(int argc, char **argv) { ... }
int main(int argc, char *argv[]) { ... } // 都可以
```

第一个形参 `argc` 表示数组中字符串数量，第二个形参 `argv` 是数组，其中 `argv[0]` 是程序的名称；从 `argv[1]` 开始是其可选实参。

```bash
prog -d -o ofile data0 
# argv[1] = "-d"; argv[2] = "-o"; ... argv[5] = "0";
# argc = 5
```



