title: Elements of Modern C++ Style
date: 2015-06-26 13:45:28
categories: [C/C++,翻译]
tags: [C++11]

---

译自：http://herbsutter.com/elements-of-modern-c-style

> "C++11 feels like a new language." -- Bjarne Stroustrup

`C++11`标准提供了[很多新特性](http://www2.research.att.com/~bs/C++0xFAQ.html)。本篇文章特别的而且仅仅专注于那些使得`C++11`相比于`C++98`来说确实感觉像一门新语言的特性，因为：

- 它们改变了写C++代码时所要使用的风格和惯用手法，通常还包括你设计C++库的方式。例如，你将会看到更多的智能指针作为参数和返回值，函数也会通过值的方式返回大对象。
- 它们被如此普遍的使用，以至于你会在大多数的代码示例中看见。例如，几乎在每5行的现代C++代码示例中你就会看见`auto`的身影。

也要使用其它好的`C++11`特性，但是先用好这些，因为它们是使得`C++11`代码整洁、安全和快速的最普遍的特性（和现代主流语言一样整洁和安全），再加上C++的传统的如金属般的性能，使之一如既往的强大。

说明：
- Like Strunk & White，本文故意专注于简洁的总结指导。不是为了提供详尽的原理阐述和pro/con分析；这将在其它的文章中探究。
- 这是一份保持更新的文档。详见最后随时间变化和添加的列表。

## auto

只要有可能就使用`auto`。它是有用的，原因有两个。首先，最明显的是它是一种便利，可以使我们避免重复一个类型的名称，即使我们已经阐明而且编译器已经知道了。

```C++
// C++98
map<int, string>::iterator i = m.begin();
double const xlimit = config["xlimit"];
singleton& s = singleton::instance();

// C++11
auto i = begin(m);
auto const xlimit = config["xlimit"];
auto& s = singleton::instance();
```

其次，当一个类型具有不可知或难以用语言来表达的名称时，它就不仅仅是一个便利了。例如，多数的lambda函数你根本不能或不能轻易地拼写它的类型名。

```C++
// C++98
binder2nd<greater> x = bind2nd(greater(), 42);

// C++11
auto x = [](int i) { return i > 42; };
```

注意使用`auto`并没有改变代码的含义。代码任然是静态类型的，而且每个表达式的类型都是清晰和明确的；语言再也不会迫使我们重申类型名。

一些人刚开始害怕在这里使用`auto`，因为如果不（重新）声明需要的类型的话，感觉好像我们可能意外的得到一个不同的类型。如果你想要显示的`强制类型转换`，声明目标类型也是可以的。然而，绝大多数时间，只使用`auto`就可以了；很少会发生因为错误而得到一个不一样的类型的情况，而且即使在这种情况下，语言的强大类型系统意味着编译器会让你知道：你在尝试调用一个变量的并不存在的成员方法，或者不是以正常的方式使用它。

## Smart pointers: No delete

始终使用智能指针和*non-owing*原始指针。绝不使用*owing*原始指针和`delete`，在罕见的情况下除外，需要实现你自己的底层数据结构（即使如此，也要在类边界里面保持良好的封装）。

如果你知道你们是另外一个对象的唯一拥有者，使用`unique_ptr`来明确唯一所有权。"`new T`"表达式应该立即初始化拥有它的另一个对象，通常使用`unique_ptr`。一个典型的例子是`Pimpl惯用手法`（见 [GotW #100](http://herbsutter.com/gotw/_100)：

```C++
// C++11 Pimpl idiom: header file
class widget {
public:
    widget();
    // ... (see GotW #100) ...
private:
    class impl;
    unique_ptr<impl> pimpl;
};

// implementation file
class widget::impl { /*...*/ };

widget::widget(): pimpl{ new impl{ /*...*/} } { }
// ...
```

使用`shared_ptr`明确共享所有权。更喜欢使用`make_shared`来有效的创建共享对象。

```C++
// C++98
widget* pw = new widget();
...
delete pw;

// C++11
auto pw = make_shared<widget>();
```

使用`weak_ptr`打破循环和明确可选性（例：实现一个对象缓存）。

```C++
.. C++11
class gadget;

class widget {
private:
    shared_ptr<gadget> g; // if shared ownership
};

class gadget {
private:
    weak_ptr<widget> w;
};
```

如果你知道另一个对象比你的生存期长，而且你需要观察它，那么使用(non-owing)原始指针。

```C++
// C++11
class node {
    vector<unique_ptr<node> > children;
    node* parent;
public:
    ...
};
```

## nullptr

始终使用`nullptr`作为空指针的值，绝不使用模棱两可的字面量`0`或者`NULL`宏，因为它们可以是一个整形或指针。

```C++
// C++98
int* p = 0;

// C++11
int* p = nullptr;
```

## Range for

使用基于范围的`for`循环的方式来对一个范围内的元素进行顺序访问是非常方便的。

```C++
// C++98
for(vector<int>::iterator i = v.begin(); i != v.end(); ++i) {
    total += *i;
}

// C++11
for(auto d: v) {
    total += d;
}
```

## Nonmenber begin and end

总是使用非成员的`begin(x)`和`end(x)`（而不是`x.begin()`和`x.end()`）。因为`begin(x)`和`end(x)`是可扩展的，可以和所有的容器类型（甚至是数组）一起使用，而且不仅仅是容器，还包括那些遵循`STL`风格，实现了`x.begin()`和`x.end()`成员函数的类型。

如果你使用了非`STL`集合类型，它们提供了迭代，但不是`STL`风格的`x.begin()`和`x.end()`，你可以编写你自己的非成员的`begin(x)`和`end(x)`来重载该类型。这样你就可以使用如上所示的`STL`容器的相同编码风格来遍历该类型。`C++11`的标准作出了榜样：C数组就是这种类型，`C++11`标准给它提供了`begin`和`end`。

```C++
vector<int> v;
int a[100];

// C++98
sort(v.begin(), v.end());
sort(&a[0], &a[0] + sizeof(a)/sizeof(a[0]));

// C++11
sort(begin(v), end(v));
sort(begin(a), end(a));
```
