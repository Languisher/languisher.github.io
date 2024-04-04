---
title: "C++ 基础（3）：类"
date: 2024-02-25T20:16:07+08:00
lastmod: 2024-02-25T20:16:07+08:00
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

## 数据结构和抽象数据类型的区别

定义类：以关键字 `struct` 或 `class` 开始（在 [访问控制和封装](#访问控制和封装) 中介绍区别），紧跟类名和类体的类，类体形成一个新的作用域。

注意：类内不能使用圆括号初始化变量。

以下是一个基础的数据结构。

```cpp
/* (1) 基础的数据结构 */
struct Sales_data {  
    // 数据成员  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
}; // 请一定注意这里的分号
```


**抽象数据类型 (abstrct data type)** 用于实现类的数据抽象和封装，其与数据结构的关系是：抽象数据 = 数据结构 + 基于类的操作。

例如，[自定义数据结构](#自定义数据结构) 中的 `Sales_data` 类因为缺少操作以供类的用户使用不是抽象数据类型。接下来以新的 `Sales_data` 类介绍如何定义抽象数据类型。

```cpp
/* (2) 基础的抽象数据类型 */
struct Sales_data {  
    // 新增的关于 Sales_data 类的操作，同时也是成员函数。
    // 正是这一部分体现了 Sales_data 是抽象数据类型，我们定义了自己的操作。  
    std::string isbn() const { return bookNo; } // 定义函数末尾不加分号
    Sales_data& combine(const Sales_data&); // 声明函数末尾加分号 
    double avg_price() const;  
    // 原本的数据成员  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
};  
  
// 非成员接口函数  
Sales_data add(const Sales_data&, const Sales_data&);  
std::ostream &print(std::ostream&, const Sales_data&);  
std::istream &read(std::istream&, Sales_data&);
```

## 类的成员函数

类的 **成员函数 (member function)** 是定义为类的一部分的函数。

成员函数的声明必须在类的内部，其定义可以在类的外部。

即使类成员定义在类的成员函数之后，成员函数也是还能调用类成员。这是因为编译器先编译成员的声明，后轮到成员函数体。

### `*this` 指针

**成员函数通过名为 `this` 额外的隐式参数访问类内的对象**，任何对类成员的直接访问都看作是 `this` 的隐式应用。`this` 本质上是一个常量指针，在 `Sales_data` 类中其类型即 `Sales_data *const`.

当调用成员函数时，即将请求该函数的对象地址初始化 `this`.

不可以显示定义 `this`，但可以使用 `this` 表示调取成员函数的类。

```cpp
// 以下两种写法是等价的
// 写法 1
struct Sales_data {
    std::string isbn() const { return bookNo; }
    ...
}
Sales_data total; 
total.isbn();
// 写法 2：成员函数实际上是通过 `this` 隐式参数访问，这里写成 `thisp` 是因为 `this` 不能被显式定义的。
std::string isbn(Sales_data *thisp) {
    return thisp->bookNo;  
}  
isbn(&total); // 相当于多传入一个参数：类的地址。
```

### 定义 `const` 成员函数


**当类对象是常量对象时**（比如说读取类的信息，不需要修改类的属性，因此将其设为常量对象），需要在成员函数的参数列表后面加 `const`，表示 `this` 是指向常量的指针。像这样使用 `const` 对象的成员函数被称为 **常量成员函数 (const member function)**. 此时 `this` 是一个指向常量的常量指针，其类型是 `const Sales_data *const` ：

⚠️常量对象，以及常量对象的引用或指针都只能调用常量成员参数。

```cpp
struct Sales_data {
    std::string isbn() const { return bookNo; }
    std::string isbn() const { return this->bookNo; } // 注意不能这么写，因为不能显示地定义 this 指针。
    // 两者等价
}

total.isbn();
Sales_data::isbn(&total); // 传入 total 地址
// 两者等价

struct Sales_data {
    double avg_price() const; // 注意此处参数列表后面的 const
}

std::string Sales_data::isbn(const Sales_data *const this) { return this->isbn} // 说明隐式的 this 如何运作，注意不能这么写，因为不能显示地定义 this 指针。
```


### 定义在类的外部

类外部定义的成员的名字必须包含它所属的类名。

```cpp
// 注意第一行写法
double Sales_data::avg_price() const {  
    if (units_sold)
        return revenue/units_sold;  
    else  
        return 0;  
}
```

**返回 `this` 对象的成员函数可以实现类对象之间 `+=` 的效果。** 内置的赋值运算符将其左侧运算对象作为左值返回，因此**函数返回引用类型**，使两者保持一致。

```cpp
Sales_data& Sales_data::combine(const Sales_data &rhs) 
// 注意返回：Sales_data&，返回引用类型。
{
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this; // 返回调用该函数的对象
}

total = total.combine(trans); // 假设两者都已定义，this 绑定 total，trans 绑定 rhs
// 效果即 total.units_sold += rhs.units_sold，结果保存至 total.units_sold
```

### 返回 `*this` 时，基于 const 的重载

**针对类对象是否是 const 的，需要重载定义类的成员函数。**（提醒：不能把 const 对象赋值给非 const 对象）

```cpp
class Screen {
public:
    // 根据对象是否是 const 重载了 display 函数
    Screen &display(std::ostream &os) { ...; return *this; }
    const Screen &display(std::ostream &os) const { ...; return *this; } // 注意两侧的 const
}

Screen myScreen;
const Screen blank;
myScreen.set('#').display(cout); // 调用非 const 版本
blank.display(cout); // 调用 const 版本
```
## 类相关的非成员函数

定义类相关的非成员函数基本同普通函数。

```cpp
Sales_data add(const Sales_data &, const Sales_data &);
Sales_data add(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs;
    sum.combine(rhs);
    return sum;
}
```


## 类的构造函数

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
    // 1. 默认构造函数，注意写法
    Sales_data() = default;
    // 2. 构造函数初始值列表
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(const std::string &s, unsigned n, double p): 
        bookNo(s), units_sold(n), revenue(p*n) { }
    // 3. 类的外部定义构造函数
    Sales_data(std::istream &);
}

// 3. 类的外部定义构造函数
Sales_data::Sales_data(std::istream &is)
{
    read(is, *this); // 读取信息，存储到 `*this`，即调取函数的类
}
```

**建议使用构造函数初始值，因为一些数据成员必须被初始化。** 例如，const 成员一旦定义并不可再赋值，必须定义时就明确其值。因此，必须通过构造函数初始值列表为这些成员提供初值。

```cpp
class ConstRef {  
public:  
    // 正确写法，const 和引用对象必须被初始化
    ConstRef(int ii): i(ii), ci(ii), ri(i) { }
private:  
    int i;  
    const int ci;  
    int &ri;  
};

ConstRef::ConstRef(int ii)
{
    // 错误，不能赋值
    i = ii;
    ci = ii;
    ri = i;
}
```

### 委托构造函数

可以通过在构造函数中调用其他构造函数，即**委托构造函数 (delegating constructor)**.

```cpp
class Sales_data {  
public:  
    // 非委托构造函数使用对应实参初始化成员  
    Sales_data(std::string s, unsigned cnt, double price):  
        bookNo(s), units_sold(cnt), revenue(cnt * price) { }  
    // 构造函数委托上面的构造函数  
    Sales_data(): Sales_data("", 0, 0) { }  
    // 构造函数委托上面的构造函数  
    Sales_data(std::istream &is): Sales_data() { read(is, *this); }  
    // read 函数  
    std::istream &read(std::istream&, Sales_data&);  
private:  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
};
```

### 默认构造函数

如果定义了其他构造函数，最好定义默认构造函数。

注意使用默认构造函数时，需要去掉对象名后的括号对。

```cpp
Sales_data obj; // 正确，定义对象，默认构造
Sales_data obj(); // 错误，定义的是函数，返回值是 Sales_data 类
```

### 转换构造函数：隐式的类类型转换

我们希望一个 string 能够被隐式转换为一个类对象，从而与其他类对象交互，因此引入了**转换构造函数 (converting constructor)**.

规则：
1. 构造函数只接受一个实参，且符合你想要的类型；
1. 只允许一步类类型转换；
1. 类类型转换不一定正确。

```cpp
class Sales_data {  
public:  
    Sales_data() = default;  
    Sales_data(std::string s):  
        bookNo(s) {}  
    Sales_data& combine(const Sales_data&);  
private:  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
};

std::string null_book = "9-999-99999-9";  
Sales_data item;  

item.combine(null_book); // 此时，构造了一个临时的对象，正确操作。
item.combine("9-999-99999-9"); // 错误，需要首先将字符串常量转换成 string 再进行类类型转换，不符合规则 2
```

若我们不希望任何构造函数隐式创建类对象，则在*类内声明*构造函数前加 `explicit`. 与此同时，类外定义时不应重复。

```cpp
// 在构造函数中添加了 explicit 关键词。
explicit Sales_data(std::string s):  
    bookNo(s) {}
```
## 访问控制和封装

### 访问控制：访问说明符

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

### 分享信息：友元

类允许其他类或函数访问它的非公有成员，方法是令其他类或函数称为其 **友元 (friend)**.

注意：
- 友元声明并非函数声明，只是指定访问的权限。
- 友元不具有传递性。若 A 是 B 的友元，A 的友元并不拥有访问 B 的特权。
- **类与重载函数**：如果一个类想把一组重载函数声明为其友元，它需要对这组函数的每一个进行声明。

```cpp
class Sales_data {
// 为 Sales_data 非成员函数所做友元声明
friend Sales_data add(const Sales_data&, const Sales_data&);
// 类之间的友元关系
friend Other_class;
// 类与其它类中的成员函数的友元关系
friend Other_class::clear();

// 类内成员
public:
    ... // 一些接口函数
private:
    ... // 一些内部数据
}

Sales_data add(const Sales_data&, const Sales_data&); // 仍需函数声明
```

## 类的声明

可以声明但不定义其成员的类，称之为 **前向声明 (forward declaration)**. 此时，类是**不完全类型 (incomplete type)**.

在完成定义之前，不可以声明类的对象。

```cpp
class Screen;
class Link_Screen {
    Screen window;
};
```
## 类成员

### 定义类型成员

若我们不希望某个变量实际上是由 `string` 对象存储它的数据，可以定义成 `public` 成员从而隐藏实现细节。

用来定义类型的成员应当先定义后使用。

```cpp
class Screen {  
public:  
    typedef std::string::size_type pos;  // 定义新的 `pos`
private:  
    pos cursor = 0;  
    pos height = 0, width = 0;  
};
```

### 可变数据成员

我们希望类对象的某一个变量始终可以被修改，无论类对象是常量对象或者在 const 成员函数内。我们在变量声明时使用 `mutable` 将其声明为 **可变数据成员 (mutable data member)** 来做到这点。

根据定义，可变数据成员永远不是 `const`.

```cpp
class Screen {
public:
    void some_member() const; // const 成员函数
private:
    mutable size_t access_str; // 即使在 const 对象中也能修改
};

void Screen::some_member() const
{
    ++access_str; // 正确：可以修改
}
```

### 静态成员

有些变量只与类本身直接相关，例如所有银行客户的存款都享受统一的基准利润，不随客户而改变。因此，我们引入 `static` 关键字来声明**静态成员**，以及静态成员函数。

规则：
- 静态成员可以是各种类型。
- 静态成员函数不能是 const, 因为静态成员函数不与对象绑定。
- 类外部*定义*静态成员函数时，不能加 `static`.


```cpp
class Account {  
public:  
    void calculate() { amount += amount * interestRate; }
    // 声明和定义静态成员函数
    static double rate() { return interestRate; }  
    static void rate(double);
private:  
    double amount;  
    // 静态成员
    static double interestRate;  
};
```

**内部初始化静态成员**：可以为静态成员
- 提供 `const` 整数类型的类内初始值
- 定义为字面值常量类型，其初始值必须是常量表达式，用 `constexpr` 声明。

```cpp
class Account {  
public:  
    // 都正确，且关键字都无法去除。
    static constexpr double rate = 6.5;  
    static const int size = 20;  
};
```


## 参考资料

本文主要基于 C++ Primer（第 5 版）所写，参考章节为第 7, 13-16 章。
