title: 《Effective Modern C++》条款4
tags: [C++11]
toc: false
---

## 条款4：了解如何查看推导出的类型(Know how to view deduced types)

### IDE 编辑器

IDE在编译状态时（如调试），把光标放在实体之上，就会显示出该实体的类型（该信息来源于IDE当前所运行的编译器）。

### 编译器诊断信息

使得编译器能够显示其推导出的类型的一种有效的方式是：以一种能够引发编译问题的方式使用该类型，在编译错误消息中就会报告引发该错误的类型。

例如，用推导的类型来实例化一个缺少定义的模板（只有声明）模板。
```C++
template<typename T>
class TD;            // TD == "Type Displayer"

const int theAnswer = 42;
auto x = theAnswer;
auto y = &theAnswer;

TD<decltype(x)> xType;
TD<decltype(y)> xType;
```

<!-- more -->

以上代码在`GNU`编译器中会报告如下编译错误：
```
error: aggregate ‘TD<int> xType’ has incomplete type and cannot be defined
error: aggregate ‘TD<const int*> yType’ has incomplete type and cannot be defined
```

### 运行时打印

推荐使用`Bosst.TypeIndex`，而不是`C++`标准库的`typeid`。

```C++
#include <boost/type_index.hpp>
#include <vector>
#include <iostream>

class Widget {};

std::vector<Widget> createVec()
{
    return vector<Widget>(10);
}

template<typename T>
void f(const T& param)
{
    using std::cout;
    using boost::typeindex::type_id_with_cvr;
    // show T
    cout << "T =     "
         << type_id_with_cvr<T>().pretty_name()
         << '\n';

    // show param's type
    cout << "param = "
         << type_id_with_cvr<decltype(param)>().pretty_name()
         << '\n';
}

int main()
{
    const auto vw = createVec();
    if(!vw.empty())
    {
        f(&vw[0]);
    }
}
```

使用`GNU`和`Clang`编译器，以上代码输出：
```
T =     Widget const*
param = Widget const* const&
```


### 要点：

- 通常可以使用IDE编辑器、编译器错误信息和`Boost`的`TypeIndex`库来查看推导出的类型
- 一些工具提供的结果可能没有用或不准确，因此理解标准`C++`的类型推导规则任然是必不可少的


注意：`TyoeIndex`库在`Boost` `1.56.0`版本中引入
