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

<!-- more -->

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

如果你知道你是另外一个对象的唯一拥有者，就使用`unique_ptr`来明确唯一所有权。"`new T`"表达式应该立即初始化拥有它的另一个对象，通常使用`unique_ptr`。一个典型的例子是`Pimpl惯用手法`（见 [GotW #100](http://herbsutter.com/gotw/_100)）：

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

## Lambda Functions and Algorithms

Lambda表达式是一个可以改变游戏规则的改变，它会经常性的改变你写代码的方式，使之更优雅和更快速。Lambda表达式使得现有的`STL`算法的可用性提升了100倍。更新的C++的库的设计越来越多的假设Lambda表达式是可用的（例：`PPL`），而且某些甚至要求你编写Lambda表达式来使用库（例：`C++ AMP`）。

这是一个简单的例子：在`v`中找到第一个大于`x`并小于`y`的元素。在`C++11`中，最简单的和最整洁的代码是使用标准算法。

```C++
// C++98: write a naked loop (using std::find_if is impractically difficult)
for(; i != v.end(); ++i) {
  if(*i > x && *i < y) break;
}

// C++11: use std::find_if
auto i = find_if(begin(v), end(v), [=](int i) {return i > x && i < y;});
```

你是否想要一个循环或类似的语言特性（但语言并没有提供）？并不需要付出多大的努力，只要把它写成一个模板函数（算法库），并利用lambda表达式你总是可以如同使用语言特性一样便利的使用它。而且具有更大的灵活性，因为它真的只是一个库，而不是一个天生的语言特性。

```C++
// C#
lock(mut_x) {
  ... use x ...
}

// C++11 without lambdas: already nice, and more flexible (e.g., can use timeouts, other options)
{
  lock_guard<mutex> hold {mut_x};
  ... use x ...
}

// C++11 with lambdas, and a helper algorithm: C# syntax in C++
// Algorithm: template<typename T> void lock(T& t, F f) {lock_guard hold(t); f();}
lock(mut_x, [&]{
  ... use x ...
});
```

要熟悉lambda表达式，你将会大量的使用它们，不仅仅是在C++中--它们已经在几种流行的主要语言中被支持和广泛使用。一个很好的起点是我在`PDC 2010`上的演讲：[Lambdas, Lambdas Everywhere](http://herbsutter.com/2010/10/30/pdc-languages-panel-andshortened-lambdas-talk)。

## Move / &&

`move`是作为`copy`优化的一个最好的思想，虽然它也可以做其他的事情，如：perfect forwarding。

`move`语义使我们设计`API`的方式发生了改变。我们将会设计出更多的返回值对象的接口。

```C++
// C++98: alternatives to avoid copying
vector<int>* make_big_vector(); // option 1: return by pointer: no copy, but don't forget to delete
...
vector<int>* result = make_big_vector();

void make_big_vector(vector<int>& out); // option2: pass out by reference: no copy, but caller needs a named object
...
vector<int> result;
make_big_vector(result);


// C++11: move
vector<int> make_big_vector(); // usuall sufficient for 'caller-allocated out' situations
...
auto result = make_big_vector(); // guaranteed not to copy the vector
```

当你做一些比`copy`更有效率的事情的时候，为你的类型启用`move`语义。

## Uniform Initialization and Initializer Lists

没有改变的是：当初始化一个局部变量，且它的类型是非`POD`或`auto`时，继续使用熟悉的 `=` 语法，而不用额外的`{ }`。

```C++
// C++98 or C++11
int a = 42;      // still fine, as always


// C++11
auto x = begin(v); // no narrowing or non-initialization is possible
```

其它情况下，特别是你以前在构造对象时经常使用的`( )`，应该使用`{ }`代替。使用大括号可以避免几个潜在的问题：你不会意外的获得收缩类型转换（例：`flot`到`int`）；你不会偶尔不小心拥有未初始化的`POD`成员变量或数组；你会避免`C++98`给你带来的偶然惊喜：由于在C++语法中的声明某凌两可（Scott Meyers 的一句名言："C++'s most vexing parse."），你实际上声明了一个函数而不是一个变量，但还是能通过编译：。在新的风格中没有任何的烦恼。

```C++
// C++98
rectangle w(origin(), extents());  // oops, declares a function, if origin and extents are types
complex<double> c(2.71828, 3.14159);
int a[] = {1,2,3,4};
vector<int> v;
for(int i = 1; i <= 4; ++i) v.push_back(i);

// C++11
rectangle w {origin(), extents()};
complex<double> c {2.71828, 3.14159};
int a[] {1,2,3,4};
vector<int> v {1,2,3,4};
```

新的`{ }`语法在每个地方都工作的很好：

```C++
// C++98
X::X(/*...*/): mem1(init1), mem2(init2, init3) {/*...*/}


// C++11
X::X(/*...*/): mem1{init1}, mem2{init2, init3} {/*...*/}
```

最后，有些时候只是为了方便，在给函数传递参数是可以不需要临时的类型名。

```C++
void draw_rect(rectangle);

// C++98
draw_rect(rectangle(myobj.origin, selection.extents));


// C++11
draw_rect({myobj.origin, selection.extents});
```

我不使用大括号的唯一地方是在对非`POD`变量进行简单初始化的时候，如：`auto x = begin(v);`，不然它会使代码将变得不必要的丑陋。因为我知道它是一个类类型，所以我知道我不需要担心偶然的收缩类型转换，而且现代的编译器已经例行的执行优化以删去额外的拷贝（或额外的移动，如果类型启用了`move`）。

## And More

现代C++还有[更多特性](http://www2.research.att.com/~bs/C++0xFAQ.html)。在将来，我打算写一些更深入的文章，关于`C++11`的这些和其它的我们要去了解和喜欢的特性。

但是现在，这里是我们必须要知道的特性列表。这些特性形成的核心，定义了现代C++的风格，这使得C++代码看起来和执行起来确实如此；你会发现在你所将要看到或编写的每一段现代C++代码中会普遍的被使用；使得现代C++是一门整洁、安全和快速的语言，在未来的许多年我们的行业将会继续严重的依赖于它。



