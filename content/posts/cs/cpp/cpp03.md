---
title: "C++ 基础（三）：类"
date: 2024-02-20T20:16:07+08:00
lastmod: 2024-02-20T20:16:07+08:00
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

类的基本思想是 **数据抽象 (data abstraction)** 和 **封装 (encapsulation)**，而 **数据抽象** 是一种依赖于 **接口 (interface)** 和 **实现 (implementation)** 分离的编程技术。

## 自定义数据结构


以关键字 `struct` 或 `class` 开始（在 [访问控制](#访问控制) 中介绍区别），紧跟类名和类体的类，类体形成一个新的作用域。

注意：类内不能使用圆括号初始化变量。

```cpp
struct Sales_data {
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
}; // 请一定注意这里的分号
```



## 定义抽象数据类型

> 以下略：
> ```cpp
> struct Sales_data {} // 定义类，会在后面的部分填充
> Sales_data total // 定义类对象
> ```
### 定义类的成员函数

成员函数的声明必须在类的内部，其定义可以在类的外部。

#### 定义在函数内部

即使类成员定义在类的成员函数之后，成员函数也是还能调用类成员。这是因为编译器先编译成员的声明，后轮到成员函数体。

```cpp
struct Sales_data {
	// 关于 Sales_data 对象的操作
	std::string isbn() const { return bookNo; } // isbn 成员函数返回对象的 ISBN 编号
	// 数据成员
	std::string bookNo; // ISBN 编号
}
```


**成员函数通过名为 `this` 额外的隐式参数访问类内的对象**，任何对类成员的直接访问都看作是 `this` 的隐式应用。`this` 本质上是一个常量指针，在 `Sales_data` 类中其类型即 `Sales_data *const`.

```cpp
struct Sales_data {
	std::string isbn() const { return bookNo; }
	std::string isbn() const { return this->bookNo; } // 注意不能这么写，因为不能显示地定义 this 指针。
	// 两者等价
}

total.isbn();
Sales_data::isbn(&total); // 传入 total 地址
// 两者等价
```


**注意类对象是否是常量对象。** 若是，则需要在参数列表后面加 `const`，表示 `this` 是指向常量的指针，此时 `this` 是一个指向常量的常量指针，其类型是 `const Sales_data *const` ：

⚠️常量对象，以及常量对象的引用或指针都只能调用常量成员参数。

```cpp
struct Sales_data {
	double avg_price() const; // 注意此处参数列表后面的 const
}

std::string Sales_data::isbn(const Sales_data *const this) { return this->isbn} // 说明隐式的 this 如何运作，注意不能这么写，因为不能显示地定义 this 指针。
```

#### 定义在函数外部

类外部定义的成员的名字必须包含它所属的类名。

```cpp
double Sales_data::avg_price() const {
	...
}
```

**返回 `this` 对象的成员函数可以实现类对象之间 `+=` 的效果。** 内置的赋值运算符将其左侧运算对象作为左值返回，因此**函数返回引用类型**，使两者保持一致。

```cpp
Sales_data& Sales_data::combine(const Sales_data &rhs) 
// 注意返回：Sales_data&，返回引用类型。
{
	units_sold += rhs.units_sold;
	return *this; // 返回调用该函数的对象
}

total = total.combine(trans); // 假设两者都已定义，this 绑定 total，trans 绑定 rhs
// 效果即 total.units_sold += rhs.units_sold，结果保存至 total.units_sold
```

### 定义类相关的非成员函数

同普通函数。

```cpp
Sales_data add(const Sales_data &, const Sales_data &);
Sales_data add(const Sales_data &lhs, const Sales_data &rhs)
{
	Sales_data sum = lhs;
	sum.combine(rhs);
	return sum;
}
```

### 类的构造函数

**构造函数 (constructor)** 是一个或几个特殊的类的成员函数用于控制其对象的初始化过程。

注意事项：
- 构造函数不能被声明是 const 的。
- 构造函数通常函数体为空，因为其唯一目的就是为数据成员赋初值。
- 构造函数尽量不要轻易覆盖类内初始值。

不同的构造函数：
- **合成的默认构造函数 (synthesized default constructor)**：默认方式初始化，其中 = default 即默认构造，注意写法。
- **构造函数初始值列表 (constructor initialize list)**：在冒号和花括号之间，为新创建的对象的一个或几个数据成员赋值，不同成员用逗号隔开。
- 类外部构造函数：函数体内调用，执行构造函数体。


```cpp
struct Sales_data {
	Sales_data() = default; // 默认构造函数，注意写法
	// 构造函数初始值列表
	Sales_data(const std::string &s): bookNo(s) { }
	Sales_data(const std::string &s, unsigned n, double p): 
		bookNo(s), units_sold(n), revenue(p*n) { }
	// 类外部
	Sales_data(std::istream &);
}

Sales_data::Sales_data(std::istream &is)
{
	read(is, *this);
}
```

## 访问控制和封装

### 访问控制

之前类用户可以直达 `Sales_data` 对象的内部并且控制它的具体实现细节。现在我们想要隐藏部分信息，可以利用 C++ 语言的 **访问说明符 (access specifiers)**:

- `public` 之后的成员在整个程序内可被访问，`public` 成员定义类的借口，以供用户使用。
- `private` 说明符之后的成员可以被 *该类的成员函数* 访问，但不能被 *使用类的代码* 访问，即封装了类的实现细节。

**`class` 和 `struct` 的区别即在于默认的访问权限。** 直到第一个访问说明符，`class` 默认是 `private` 而 `struct` 默认是 `public`. 当我们希望类内所有成员是 private 时使用 `class`，反之使用 `struct`.

```cpp
class Sales_data {
public:
	Sales_data(const std::string &s): bookNo(s) { } // 初始化接口以供使用，因此是 public（后文还会介绍）
private:
	unsigned units_sold = 0; // 隐藏内部数据，因此是 private
	double revenue = 0.0; // 隐藏内部数据
}
```

### 分享信息

类允许其他类或函数访问它的非公有成员，方法是令其他类或函数称为其 **友元 (friend)**.

注意：友元声明并非函数声明，只是指定访问的权限。

```cpp
class Sales_data {
// 为 Sales_data 非成员函数所做友元声明
friend Sales_data add(const Sales_data&, const Sales_data&);
public:
	... // 一些接口函数
private:
	... // 一些内部数据
}

Sales_data add(const Sales_data&, const Sales_data&); // 仍需函数声明
```


