title: 《Effective Modern C++》条款1
tags: [C++11, 翻译]
toc: false
---

## 条款1：理解模板类型推导(Understand template type deduction)

模板的类型推导是现代`C++`最引人注目的特性之一`auto`的基础，因此要真正理解模板类型推导的各个方面。

函数模板：
```C++
template<typename T>
void f(ParamType param);
```

调用方式：
```C++
f(expr);  // call f with some expression
```

在编译过程中，编译器使用`expr`来推导两个类型：`T`、`ParamType`。被推导出的`T`的类型不仅取决于`expr`的类型，还取决于`ParamType`的形式。有三种情形：

<!-- more -->

- 情况1：`ParamType`是一个引用或指针，但不是一个万能引用(Universal Reference)

  类型推导的工作方式如下：
  1. 忽略`expr`的类型的引用或指针部分（即去掉`&`或`*`）
  2. 然后，模式匹配`expr`的类型和`ParamType`来获得`T`

  例1：
  ```C++
  template<typename T>
  void f(T& param);    // param is a reference

  int x = 27;
  const int cx = x;
  const int& rx = x; 

  const char * ptrToName = name;
  const char name[] = "J. P. Briggs"; // 数组衰变成指针

  void someFunc(int, double);  // 函数类型会衰变成函数指针


  f(x);    // T is int, param's type is int&
  f(cx);   // T is const int, param's type is const int&
  f(rx);   // T is const int, param's type is const int&

  f(name); // T is const char[13], param's type is const char (&)[13]

  f(someFunc); // T is void (*)(int, double)
  ```

  例2：
  ```C++
  template<typename T>
  void f(const T& param);   // param is now a ref-to-const

  int x = 27;
  const int cx = x;
  const int& rx = x;

  f(x);     // T is int, param's type is const int&
  f(cx);    // T is int, param's type is const int&
  f(rx);    // T is int, param's type is const int&
  ```

- 情况2：`ParamType`是一个万能引用(Universal Reference)

  - 如果`expr`是一个左值(lvalue)，`T`和`ParamType`都被推导成左值引用。
  - 如果`expr`是一个右值(rvalue)，使用一般的推导规则（情况1中所示）。

  例：
  ```C++
  template<typename T>
  void f(T&& param);    // param is now a unicersal reference

  int x = 27;
  const int cx = x;
  const int& rc = x;

  f(x);   // x is lvalue, so T and param's type are both int& 
  f(cx);  // cs is lvalue, so T and param's type are both const int&
  f(rx);  // rc is lvalue, so T and param's type as both const int&
  f(27);  // 27 is rvalue, so T is int, param's type is int&&
  ```

- 情况3：`ParamType`既不是指针也不是引用

  这种情况下，进行的是按值传递。按值传递会拷贝一个全新的对象，该对象会忽略`expr`的修饰符（`const`和`volatile`)。其推导方式如下：
  1. 忽略`expr`类型的引用部分
  2. 忽略`expr`类型的`const`或`volatile`

  例：
  ```C++
  template<typename T>
  void f(T param);     // param is passed by value

  int x = 27;
  const int cx = x;
  const int& rx = x;
  const char* const ptr = "Fun with pointers";

  const char * ptrToName = name;
  const char name[] = "J. P. Briggs"; // 数组衰变成指针

  void someFunc(int, double);  // 函数类型会衰变成函数指针

  f(x);    // T is int
  f(cx);   // T is int
  f(rx);   // T is int
  f(ptr);  // T is const char*

  f(name); // T is const char*

  f(someFunc); // T is void (&)(int, double)
  ```

### 要点：

- 在模板类型推导过程中，引用实参的引用性被忽略
- 对万能引用(universal reference)参数进行类型推导时，左值实参产生左值引用，右值实参产生右值引用
- 对按值传递的参数进行类型推导时，忽略实参的`const`和/或`volatile`修饰符
- 对按值传递的参数进行类型推导时，数组和函数实参将退化为指针
