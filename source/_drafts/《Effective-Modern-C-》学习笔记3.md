title: 《Effective Modern C++》条款3
tags: [C++11]
toc: false
---

## 条款3：理解`decltype`(Understand `decltype`)

`decltype`可以告诉你一个变量或一个表达式的类型。

### 一般情况下，`decltype`返回的类型和你所给变量或表达式的类型一模一样

```C++
const int i = 0;          // decltype(i) is const int
bool f(const Widget& w);  // decltype(w) is const Widget&
                          // decltype(f) is bool(const Widget&)

struct Point {
int x, y;
};                        // decltype(Point::x) is int
                          // decltype(Point::y) is int

Widget w;                 // decltype(w) is Widget

if(f(w)) ...              // decltype(f(w)) is bool


template<typename T>      // simplified version of std::vector
class vector {
public:
    ...
    T& operator[](std::size_t index);
    ...
};

vector<int> v;           // decltype(v) is vector<int>
...
if (v[0] == 0) ...       // decltype(v[0]) is int&
```

<!-- more -->

在`C++11`中，`decltype`主要被用在那些其函数返回值依赖于其参数类型的函数模板声明中。

```C++
template<typename Container, typename Index>
auto authAndAccess(Container& c, Index i)
    -> decltype(c[i])
{
    authenticateUser();
    return c[i];
}
```

在`C++14`中，所有的`lambda`和函数的返回类型都可以被推导（`C++11`只支持单语句的`lambda`的返回类型推导）。因此，在`C++14`中，上例可以被写为：

```C++
template<typename Container, typename Index>
auto authAndAccess(Container& c, Index i) 
{
    authenticateUser();
    return c[i];        // 返回类型从c[i]中推导
}
```

在[条款2][Item 2]中说到，对函数返回值中的`auto`，编译器采用模板类型推导。对于大多数的容器来说，`c[i]`的类型为`T&`，但根据[条款1][Item 1]中所述，在模板类型推导过程中，初始化表达式的常量性(&)被忽略，则本例中`authAndAccess`函数的返回值就为`int`，而不是所期望的`int&`。

为了使得`authAndAccess`函数所我们期望的一样，在`C++14`中，我们需要使用`decltype`类型推导来推导出其返回值类型。
```C++
template<typename Container, typename Index>
decltype(auto)
authAndAccess(Container& c, Index i)
{
    authenticateUser();
    return c[i];
}
```

`decltype(auto)`含义：
    `auto`指示类型需要被推导，`decltype`指示在类型推导过程中使用`decltype`的规则。

当你在初始化表达式中想要使用`decltype`推导规则时，可以使用`decltype(auto)`来声明变量。

```C++
Widget w;
const Widget& cw = w;

auto myWidget1 = cw;            // auto类型推导：myWidget1的类型为Widget

decltype(auto) myWidget2 = cw;  // decltype类型推导：myWidget2的类型为const Widget&
```

再来看一下，`authAndAccess`函数在`C++14`中的声明：

```C++
template<typename Container, typename Index>
decltype(auto) authAndAccess(Container& c, Index i);
```

该函数只接受左值，而不接受右值（因为，右值不能绑定到一个非常量左值引用:`Container&`）。为了让该函数既接受左值，又接受右值，一个方法是重载该函数（一个重载版本声明为左值引用参数，另一个重载版本声明为右值引用参数），但是这带来的缺点是：需要维护两个函数。另一种避免出现这种情况的方式是：使用*万能引用*，见[条款24][Item 24]。

```C++
template<typename Container, typename Index>
decltype(auto) authAndAccess(Container&& c,
                             Index i);
```

然而，根据[条款25][Item 25]，需要在万能引用上使用`std::forward`：
```C++
template<typename Container, typename Index>
decltype(auto)
authAndAccess(Container&& c, Index i)
{
    authenticateUser();
    return std::forward<Container>(c)[i];
}
```

同理，对于`C++11`，也要如此：
```C++
template<typename Container, typename Index>
auto
authAndAccess(Container&& c, Index i)
-> decltype(std::forward<Container>(c)[i])
{
    authenticateUser();
    return std::forward<Container>(c)[i];
}
```


### 对于类型为`T`的左值表达式，`decltype`返回`T&`

```C++
int x = 0;  // decltype(x) is int, decltype((x)) is int&

decltype(auto) f1()
{
    int x = 0;
    ...
    return x;  // decltype(x) is int, so f1 return int
}

decltype(auto) f2()
{
    int x = 0;
    ...
    return (x);  // decltype((x)) is int&, so f1 return int&
}
```

`f2`返回值是`int&`，而且是局部变量的引用！**小心该坑**

### 要点：

- `decltype`几乎总是产生和变量或表达式一模一样的类型
- 对于类型为T的左值表达式，`decltype`返回的类型总是`T&`
- `C++14`支持`decltype(auto)`，像`auto`一样，从初始化对象中推导出类型，但是它使用`decltype`的规则进行推导


[Item 1]: /《Effective-Modern-C++》学习笔记1
[Item 2]: /《Effective-Modern-C++》学习笔记2
[Item 24]: /《Effective-Modern-C++》学习笔记24
[Item 25]: /《Effective-Modern-C++》学习笔记25
