title: C++ Has Become More Pythonic
date: 2015-06-25 19:14:33
categories: C/C++
tags: [C++11,Python]
photos:
- http://preshing.com/images/pythonic_cpp.png

---

近些年C++ 发生了很多变化。最近的两个版本，`C++11` 和 `C++14`引入了如此多的特性，
就像`Bjarne Stroustrup`（C++之父）所说的一样：“[它感觉像一门新的语言](http://www.stroustrup.com/C++11FAQ.html#think)”。

确实是这样。现代C++适合于一种全新的编程风格——使我不得不注意到它更有种`Python`的味道。
基于range的`for`循环、类型推导、vector和map的初始化、lambda表达式。随着你更深入的探索现代C++，
你会发现更多Python的痕迹在里面。

现代C++直接受Python的影响吗？或者仅仅是Python在C++之前使用了这些设计？有你来裁判。

## Literals

Python在2008年引入了[二进制字面值](https://docs.python.org/dev/whatsnew/2.6.html#pep-3127-integer-literal-support-and-syntax)。现在[`C++14`拥有了它们](http://en.cppreference.com/w/cpp/language/integer_literal)。

```C++
static const int primes = 0b10100000100010100010100010101100;
```

Python早在1998年引入了[原始字符串字面值](https://www.python.org/download/releases/1.5/whatsnew/)，它们在硬编码正则表达式或Windows路径时很方便。
[`C++11`](http://en.cppreference.com/w/cpp/language/string_literal)略微不同的语法加入了同样的想法：

```C++
const char* path = R"(c:\this\string\has\backslashes)";
```

## Range-Based For Loops

在Python中，`for`循环总是在一个Python对象上迭代：

```Python
for x in myList:
    print(x)
```

与此同时，将近30年，C++只支持C风格的`for`循环。最终，在`C++11`中，[基于range的`for`循环](http://en.cppreference.com/w/cpp/language/range-for)被添加：

```C++
for (int x : myList)
    std::cout << x;
```

## Auto

Python一直是一种动态类型语言。任何地方，你都不用申明变量的类型，因为类型
是对象自己的属性。

```Python
x = "Hello world!"
print(x)
```

另一方面，C++不是动态类型的，它是静态类型语言。但是自从`C++11`使用[`auto'](http://en.cppreference.com/w/cpp/language/auto)
关键字来进行类型推导以来，你能够看起来像动态类型一样来编写代码：

```C++
auto x = "Hello world!";
std::cout << x;
```



