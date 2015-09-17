title: 《Effective Modern C++》条款2
toc: false
mathjax: false
tags:
  - C++11
date: 2015-09-17 16:54:06
categories:
---


## 条款2：理解`auto`类型推导(Understand `auto` type deduction)

除了在一种情况下，`auto`类型推导和模板类型推导是一样的。

在[条款1][Item 1]中，使用以下统一的函数模板：
```C++
template<typename T>
void f(ParamType param);
```

和统一调用方式：
```C++
f(expr);
```

在函数是`f`的调用过程中，编译器使用`expr`来推断`T`和`ParamType`。

当一个**变量**使用`auto`来声明时，`auto`扮演了模板中`T`的角色，且该**变量**的类型说明符扮演了`ParamType`的角色。因此，同[条款1][Item 1]中一样，根据**变量**的类型说明符的形式分三种情况：

- 情况1：类型说明符是一个引用或指针，但不是一个万能引用(Universal Reference)
- 情况2：类型说明符是一个万能引用(Universal Reference)
- 情况3：类型说明符既不是指针也不是引用

这三种情况于[条款1][Item 1]中的三种情况一一对应。

```C++
auto x = 27;           // x is int
const auto cx = x;     // cx is const int
const auto& rx = x;    // rx is const int&

auto&& uref1 = x;      // uref1 is int&
auto&& uref2 = cx;     // uref2 is const int&
auto&& uref3 = 27;     // uref3 is int&&

const char name[] = "R. N. Briggs";
auto arr1 = name;      // const char*
auto& arr2 = name;     // const char (&)[13]

void someFunc(int, double);
auto func1 = someFunc;   // void (*)(int, double)
auto& func2 = someFunc;  // void (&)(int, double)
```

**`auto`类型推导和模板类型推导的不同之处在于：**
    如果一个`auto`声明的变量使用*花括号初始化*，其推断的类型是一个`std::initializer_list`的实例（模板实例）。但，如果把该*花括号初始化*传入到相应的模板中，类型推断将会失败，代码不会通过编译。

```C++
auto x = {11, 23, 9};   // x is std::initializer_list<int>
// 或
// auto x{11, 23, 9};

template<typename T>
void f(T param);

f({11, 23, 9})         // 错误，不能推断出T
```

不过，可以指定模板的`param`是一个`std::initializer_list<T>`，模板类型推断就能推断出`T`。

```C++
template<typename T>
void f(std::initializer_list<T> initList);

f({11, 23, 9});  // T is int
```

**对于`C++14`，还有一点应该注意的是：**
    `auto`作为函数返回值和`lambda`的参数声明时，应该使用模板类型推导，而不是`auto`类型推导。因此，下面的示例将不会通过编译。

```C++
auto createInitList()
{
    return {1, 2, 3};
}


std::vector<int> v;
auto resetV =
    [&v](const auto& newValue) {v = newValue;};
resetV({1, 2, 3});
```

### 要点：

- 通常情况下，`auto`类型推导和模板类型推导是一样的，但是`auto`类型推导假定*花括号初始化*代表一个`std::initializer_list`，而模板类型推导不会这样做。
- 在函数返回值或`lambda`参数中的`auto`意味着模板类型推导，而不是`auto`类型推导。

[Item 1]: /《Effective-Modern-C++》学习笔记1
