title: 《Effective Modern C++》条款3
tags: [C++11]
toc: false
---

## 条款3：理解`decltype`(Understand `decltype`)

`decltype`可以告诉你一个变量或一个表达式的类型。

- 一般情况下，`decltype`返回你所给变量或表达式的类型
例：
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
In C++11, perhaps the primary use for decltype is declaring function templates
where the function’s return type depends on its parameter types. 

在`C++11`中，`decltype`主要被用在那些其函数返回值依赖于其参数类型的函数模板声明中。

例：
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
    return c[i];
}
```



### 要点：


[Item 1]: /《Effective-Modern-C++》学习笔记1

[Item 2]: /《Effective-Modern-C++》学习笔记2
