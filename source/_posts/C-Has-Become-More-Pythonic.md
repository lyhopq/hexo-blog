title: C++ Has Become More Pythonic
date: 2015-06-25 19:14:33
categories: [C/C++,翻译]
tags: [C++11,Python]
photos:
- http://preshing.com/images/pythonic_cpp.png

---

译自：http://preshing.com/20141202/cpp-has-become-more-pythonic


近些年C++ 发生了很多变化。最新的两个版本，`C++11` 和 `C++14`引入了如此多的新特性，正如`Bjarne Stroustrup`所说的：[“它感觉就像一门新的语言”](http://www.stroustrup.com/C++11FAQ.html#think)。

确实是这样。现代C++形成了一种全新的编程风格——使我不得不注意到它带有的更多`Python`的味道。基范围的`for`循环、类型推导、vector和map的初始化、lambda表达式。随着你更深入的探索现代C++，你越会发现Python的痕迹在里面。

现代C++直接受Python的影响吗？或者仅仅是Python在C++之前使用了这些设计？由你来判断。

<!-- more -->

## Literals

Python在2008年引入了[二进制字面值](https://docs.python.org/dev/whatsnew/2.6.html#pep-3127-integer-literal-support-and-syntax)。现在[`C++14`拥有了它们](http://en.cppreference.com/w/cpp/language/integer_literal)。

```C++
static const int primes = 0b10100000100010100010100010101100;
```

Python早在1998年引入了[原始字符串字面值](https://www.python.org/download/releases/1.5/whatsnew/)，它们在硬编码正则表达式或Windows路径时很方便。[`C++11`](http://en.cppreference.com/w/cpp/language/string_literal)使用略微不同的语法加入了同样的想法：

```C++
const char* path = R"(c:\this\string\has\backslashes)";
```

## Range-Based For Loops

在Python中，`for`循环总是在一个Python对象上迭代：

```Python
for x in myList:
    print(x)
```

与此同时，将近30年，C++只支持C风格的`for`循环。最终，在`C++11`中，[基于范围的`for`循环](http://en.cppreference.com/w/cpp/language/range-for)被添加：

```C++
for (int x : myList)
    std::cout << x;
```

## Auto

Python一直是一种动态类型语言。任何地方，你都不用声明变量的类型，因为类型是对象自己的属性。

```Python
x = "Hello world!"
print(x)
```

另一方面，C++不是动态类型的，它是静态类型语言。但是从`C++11`使用[`auto'](http://en.cppreference.com/w/cpp/language/auto)关键字进行类型推导以来，你能够看起来像动态类型一样来编写代码：

```C++
auto x = "Hello world!";
std::cout << x;
```

当你调用那些被好几种类型重载的函数，例如`std::ostream::operator<<`，或模板函数的时候，C++就更表现出动态类型语言的特征。`C++14`更进一步扩充了对`auto`关键字的支持，增加了对`auto`[返回值](http://en.wikipedia.org/wiki/C%2B%2B14#Function_return_type_deduction)和
lambda函数使用`auto`[参数](http://en.wikipedia.org/wiki/C%2B%2B14#Generic_lambdas)的支持。

## Tuples

Python几乎从开始就支持[`tuple`](https://docs.python.org/release/1.4/ref/ref3.html)。当你需要把几个值打包在一起的时候表现的非常友好，而不需要命名一个类。

```Python
triple = (5,6,7)
print(triple[0])
```

C++在`C++11`中把`tuple`添加到了标准库中。在[这一提议](http://open-std.org/jtc1/sc22/wg21/docs/papers/2002/n1403.pdf)中甚至提到是从Python中得到的灵感：

```C++
auto triple = std::make_tuple(5,6,7);
std::cout << std::get<0>(triple);
```

Python可以让你解包一个`tuple`到分开的变量中：

```Python
x,y,z = triple
```

你在C++中使用`std::tie`也可以做到同样的事情：

```C++
std::tie(x,y,z) = triple;
```

## Uniform Initialization

在Python中，[`list`](https://docs.python.org/2/tutorial/introduction.html#lists)是一个内建类型。这样，你可以使用单一的表达式来创建一个Python列表：

```Python
myList = [6,3,7,8]
myList.append(5);
```

C++中的`std::vetcor`是Python列表的最相近的模仿。在`C++11`中新添加了[统一初始化](http://www.stroustrup.com/C++11FAQ.html#init-list)，现在也可以让我们使用单一的表达式来创建它们：

```C++
auto myList = std::vector<int>{6,3,7,8};
myList.push_back(5);
```

在Python中，你也可以使用单一的表达式创建一个[字典](https://docs.python.org/2/tutorial/datastructures.html#dictionaries)：

```Python
myDict = {5: "foo", 6: "bar"};
print(myDict[5])
```

同样的，统一初始化也可以工作在C++的`std::map`和`unordered_map`：

```C++
auto myDict = std::unordered_map<int, const char*>{{5, "foo"}, {6, "bar"}};
std::cout << myDict[5];
```

## Lambda Expressions

自从1994年开始Python就支持了[lambda函数](http://www.artima.com/weblogs/viewpost.jsp?thread=98196)：

```Python
myList.sort(key = lambda x: abs(x))
```

[lambda表达式](http://www.stroustrup.com/C++11FAQ.html#lambda)在`C++11`中被添加：

```C++
std::sort(myList.begin(), myList.end(), [](int x, int y){return std::abs(x) < std::abs(y);});
```

在2001年，Python增加了[静态嵌套作用域](https://docs.python.org/2/whatsnew/2.2.html#pep-227-nested-scopes)，它可以使得lambda函数捕获在外围函数中定义的变量：

```Python
def adder(amount):
  return lambda x: x + amount
...
print(adder(5)(5))
```

同样的，C++的lambda表达式支持一套灵活的[捕获规则](http://en.cppreference.com/w/cpp/language/lambda#Lambda_capture)，使你能够做到相似的事情：

```C++
auto adder(int amount) {
  return [=](int x){return x + amount;};
}
...
std::cout << adder(5)(5);
```

## Standard Algorithms

Python的内建`filter`函数使你从列表有选择性的拷贝元素（但推荐使用[列表推导](https://docs.python.org/3/whatsnew/2.0.html#list-comprehensions)）：

```Python
result = filter(lambda x: x >= 0, myList)
```

`C++11`引入了[std::copy_if](http://en.cppreference.com/w/cpp/algorithm/copy)，它使我们可以使用相似的，几乎是函数式的风格：

```C++
auto result = std::vector<int>{};
std::copy_if(mylist.begin(), myList.end(), std::back_inserter(result), [](int x){return x >= 0;});
```

其它C++[算法](http://en.cppreference.com/w/cpp/algorithm)借鉴了Python内建的`transform`，`any_of`，`all_of`，`min`和`max`函数。即将到来的[rangs提议](https://github.com/ericniebler/range-v3/blob/master/doc/D4128.md)将更进一步的简化这些表达式。

## Parameter Packs

Python在[1998](https://docs.python.org/release/1.5/tut/node29.html)年开始支持任意参数列表。你可以定义一个函数，它可以接受可变数量的参数。这些参数表现为一个`tuple`，当把它们传给另一个
函数时`tuple`会被展开：

```Python
def foo(*args):
  return tuple(*args)
...
triple = foo(5,6,7)
```

`C++11`添加了对[参数包](http://www.stroustrup.com/C++11FAQ.html#variadic-templates)的支持。不同于C风格的可变参数，它和Python的任意参数列表很像，参数包有一个名字来代表整个参数序列。一个重要的区别在于：C++的参数包在运行时并不表现为一个单一的对象。你只有通过模板元编程技术在在编译时来操纵它们。

```C++
template <typename... T> auto foo(T&&... args) {
  return std::make_tuple(args...);
}
...
auto triple = foo(5,6,7);
```

并非所有`C++11`和`C++14`的新特性都借鉴于Python，但其中很大一部分看起来似乎如此。Python被认为是一个友好的、平易近人的编程语言，或许它的一些魅力已经开始暗淡了？

你觉得如何？C++的这些新特性是否使C++变得更简单、更平易近人或者更富有表达力呢？
