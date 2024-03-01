---
title: "C++ 基础（四）：指针"
date: 2024-03-02T00:42:44+08:00
lastmod: 2024-03-02T00:42:44+08:00
author: ["Languisher"]
keywords: 
- 
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
---

本文简单介绍 C++ 使用指针实现**动态分配**的特性：在使用前分配，在程序结束时销毁。

## 动态内存与智能指针

动态内存的管理通过 `new` 和 `delete` 完成：
- `new` 首先为对象分配空间，然后返回一个指向该对象（空间）的指针
- `delete` 接受指向一个动态对象的指针，销毁该对象，并且释放与之关联的内存。

这样简单的实现在实践中出现了大量的问题，其中最严重的是程序员忘记显示销毁动态对象。因此，C++ 中使用**智能指针 (smart pointer)** 类型管理动态对象。

智能指针定义在 `memory` 头文件中。

```cpp
#include <memory>
```

智能对象中主要包括：
- 多个指针指向同一个对象：`shared_ptr`
- 指针“独占”所指向的对象：`unique_ptr`
- 指针指向 `shared_ptr` 管理的对象：`week_ptr`

### shared_ptr

`shared_ptr` 指针生命周期：
1. *创建*：创建一个 `shared_ptr` 智能指针，需要提供指针可以指向的对象。
2. *计数*：每个 `shared_ptr` 都有一个关联的计数器，称之为**引用计数 (reference count)**，其会记录有多少个其他 `shared_ptr` 同时指向相同的对象。
	- 计数增加：`shared_ptr` 被用作初始化其他变量；被函数作为参数传递
	- 计数减少：`shared_ptr` 被复制；`shared_ptr` 被销毁，例如离开其局部作用域
3. *销毁*：引用计数为 0 时，`shared_ptr` 就会自动销毁所管理的对象以及相关联的内存。

由此可见，`shared_ptr` 在无用之后不再保留，*智能*地销毁了动态对象。


```cpp
// 1. 创建
shared_ptr<string> p1; // 可以指向 string 的 shared_ptr
shared_ptr<list<int>> p2; // 可以指向 list<int> 的 shared_ptr

// 2. 计数
auto p = make_shared<int>(23);  // p 一个引用者
auto q(p); // 计数增加：两个引用者
auto r = make_shared<int>(23);  
r = q; // 计数减少：r 原来的对象已经有没有引用者
	   // 在这种情况下，r 原来的对象被销毁

// 在函数中理解 shared_ptr
void process(const shared_ptr<int> ptr) {  
    cout << *ptr << endl; 
    // ptr 离开了作用域，被销毁，引用计数-1 
}  
  
int main() {  
    shared_ptr<int> p(make_shared<int>(42)); // 引用计数=1 
    process(p);  // 在 process 中引用计数=2
    int i = *p;  // 引用计数=1
    cout << i << endl;  
}
```


⚠️注意：某些类分配的资源具有与原对象相独立的生存期，当发生对象间的拷贝时，实际上是对象及其拷贝*引用相同的底层元素*。这便于我们使用动态内存在多个对象间共享数据。

与 `shared_ptr` 相关的一些操作：
- 给 `shared_ptr` 赋一个新的对象，同时更新引用计数，如有可能释放原对象：`.reset(NEW_PTR)`
- 检查是否是当前对象的唯一用户：`.unique()`.

```cpp
if(!p.unique())  
    p.reset(new int(*p)); // 分配新的拷贝
```

### 直接管理内存：new

分配内存：使用 `new` 返回一个指向该对象的指针，可以直接初始化或列表初始化值。

通过 `delete` 来将动态内存还给系统。注意：指针必须指向动态分配的内存或者是空指针，**空悬指针 (dangling pointer)** 即已经被释放了内存的指针不可以被再次释放。将空悬指针手动赋值 `nullptr` 在该内存被多个指针指向的时候会出现问题。

```cpp
int *pi = new int;  
string *ps = new string(10, '9');
auto p1 = new auto(ps); // 使用 auto 推断，但仅有单个初始化器才有效
delete pi;
```

### unique_ptr

一个 `unique_ptr` 独占其所指向的对象。其生命周期：
1. *创建*：定义一个 `unique_ptr`，需要将其绑定至一个 `new` 返回的指针上，且直接初始化。
2. *放弃控制权*：放弃对指针的控制权：`release`
3. *转移控制权*：通过结合 1 和 2 实现。
4. *释放*：释放其指向的对：`reset`.

```cpp
// 1. 创建
unique_ptr<int> p1(new int(42));

// 2+3. 转移控制权
unique_ptr<int> p2(p1.release());
	// 1. p1 置为空
	// 2. p2 被初始化为 p1 原来保存的指针
	// 实现了 p1 权限转交给了 p2 的操作

// 4. 释放内存
unique_ptr<int> q(new int(24));
p2.reset(); // 释放了其原来所指向的内存。
p2.reset(q.release()); // 释放内存+指向新对象（即 q 原对象）
```

根据其定义，
- `unique_ptr` 不支持拷贝或赋值；但有一个例外，当 `unique_ptr` 被销毁时，可以被拷贝或赋值。

```cpp
unique_ptr<int> clone(int p) {  
    unique_ptr<int> ret(new int(p));  
    return ret; // 返回一个局部对象的拷贝
}
```


