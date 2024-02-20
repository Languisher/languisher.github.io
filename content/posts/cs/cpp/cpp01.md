---
title: "C++ 基础（一）：基本类型和复合类型"
date: 2024-02-18T17:25:18+08:00
lastmod: 2024-02-20T15:00:00+08:00
author: ["Languisher"]
keywords: 
- 
categories: # 没有分类界面可以不填写
- 
tags: # 标签
- C++
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

## 基本类型
## 复合类型
### 引用

**引用 (reference)** 即别名：引用只是为一个已经存在的对象起了另外一个名字，引用类型引用另外一种类型。引用并非对象。

```c++
int ival = 1024;
int &refVal = ival; // refVal 指向 ival，即是 ival 的另外一个名字。
```

注意事项：
- 定义引用时，程序把引用和它的初始值 *绑定* 在一起，而不是将初始值 *拷贝* 给引用。
- 无法令引用重新绑定到另外一个对象。
- 因此，引用必须初始化。
- 引用不能为字面值或某个表达式的计算结果绑定在一起。

以下是一些常见错误。

```c++
int &refVal1; // 错误，没有初始化。
int &refVal2 = 100; // 错误，引用类型的初始值必须是对象。
double dval = 3.14;
int &refVal3 = dval; // 错误，引用类型初始值必须是 int 型对象。
```
### 指针

**指针 (pointer)** 基本同 C 语言的指针。

## const 限定符

const 限定符用于我们希望值不能被改变的变量。因为其值不能被改变，因此 const 对象必须被初始化。

**const 类型的对象的主要限制**：只能在该对象上执行不改变其内容的操作。

```c++
const int bufSize = 512;
const int i = get_size(); // 正确，可以是任意复杂的表达式。
```

由于 `const` 对象创建后值不再改变，因此 **`const` 对象创建后必须初始化**。

在多个文件中出现的 `const` 对象：

- 若程序包含多个文件，为了满足每个文件在调用该对象时都能访问到他的初始值，为了防止重复定义，**`const` 对象仅在文件内有效，不同文件的同名对象实际上是不同的对象**。
- 当我们希望一个对象在文件之间共享 `const` 属性时，解决的方法是：**无论是定义还是声明对象，都添加 `extern` 关键字**。

```cpp
const int bufSize; // 错误，没有初始化
// file1.cc 定义并初始化常量，且能被其他文件访问
extern const int bufSize = fcn()
// file1.h 声明，且与前者变量是同一个
extern const int bufSize;
```

### const 的引用

**常量引用** 是对 const 变量的引用。本质上，引用本身就是常量，因为一旦绑定之后不可再改变。

⚠️ **无法让一个非常量引用指向一个常量对象**，否则就可以通过引用修改 const 变量。

常量引用仅对引用可参与的操作进行限定，引用“自以为是”其引用的变量是常量，而实际上被引用的变量可以是非 const.

```cpp
const int ci = 1024, int cj = 24;

const int &ri = ci; // 正确
int &rj = ci; // 错误，非常量引用无法指向 const 对象
const int &rk = cj; // 正确，引用对象可以非 const
```

### const 的指针

**指向常量的指针**不能用于改变其对象的值，同常量引用；**常量指针**指指针作为一个变量，其值（即存放在指针中的那个地址）无法再修改，将一直指向那个位置。

无法通过**指向常量的指针**修改其存放的对象的值，而对象本身可以不是一个常量。

常量指针不与常量引用对应，与之对应的是指向常值的指针，这点也可以从缩写中看出来。

```cpp
int errNumb = 0;  

const int * curErr = &errNumb; // curErr 无法修改 errNumb 的值
int *const currErr = &errNumb; // currErr 将一直指向 errNumb，常量指针
const int *const currrErr = &errNumb; // 指向常量的常量指针，同时满足以上两点
```

### 顶层 const 和底层 const

对于任意对象而言，**顶层 const** 指其本身（比如指针本身）是常量，**底层 const** 指其基本类型（比如指针指向的对象）是常量。

**拷贝需要注意底层 const 限制**：当拷贝时，顶层 const 几乎不影响操作，但底层 const 限制不能忽视——非常量可以转化为常量，反之不行。

```cpp
int i = 0, j = 0;  
int *const p1 = &i; // 顶层 const：不能改变 p1 的值
const int *p2 = &i; // 底层 const：其基本类型，即所指的对象是常量
int *p = p2; // 错误，注意拷贝时的性质，p2 有底层 const 限制而 p 没有  
p2 = &j; // 正确，反之可行
```

### 常量表达式和 constexpr

**常量表达式 (const expression)** 指值不会改变且编译时即可以得到计算结果的表达式。函数运算结果的值一定不是常量表达式。

可以在编译时就得到计算结果的类型称之为**字面值类型**，算术类型、引用和指针属于字面值类型，而像 string 类型就不属于字面值类型。


```cpp
const int max_files = 20;  
const int limit = max_files + 1;  
int staff_size = 27; // 不是常量表达式，因为不是常值  
const int sz = get_size(); // 不是常量表达式，因为不能在编译时得到
```

如果认定变量是常量表达式，就将其声明为 `constexpr`，来验证其值是否是常量表达式。

⚠️ `constexpr` 将对象设置为顶层 `const`，因此 `const int *` 不等于 `constexpr int *`. 前者是指向常量的指针，后者是常量指针。

```cpp
constexpr int sz = size(); // 仅当 size() 是 constexpr 函数
```

