title: 《Effective Modern C++》条款5
tags: [C++11]
toc: false
---

## 条款5：使用`auto`替代显示类型声明(Prefer `auto` to explicit type declarations)

使用`auto`的几个好处：

- 不会忘记初始化变量

  `auto`是使用变量的初始值来推导变量的类型的，因此使用`auto`声明的变量必须被显示初始化。
  ```C++
  int x1;       // 可能不会被初始化（局部变量）
  auto x2;      // 错误：必须初始化
  auto x3 = 0;  // 正确
  ```

- 能够表示只有编译器能够理解的类型
  
    ```C++
  // C++11
  auto derefUPLess =
    [](const std::unique_ptr<Widget>& p1,
       const std::unique_ptr<Widget>& p2)
  { return *p1 < *p2; };

  // C++14
  auto derefLess =
    [](const auto& p1,
       const auto& p2)
  { return *p1 < *p2; };
  ```

- 直接持有闭包

  ```C++
  // No
  auto derefUPLess =
    [](const std::unique_ptr<Widget>& p1,
       const std::unique_ptr<Widget>& p2)
  { return *p1 < *p2; };

  // Yes
  std::function<bool(const std::unique_ptr<Widget>&,
                     const std::unique_ptr<Widget>&)>
    derefUPLess = [](const std::unique_ptr<Widget>& p1,
                     const std::unique_ptr<Widget>& p2)
                    { return *p1 < *p2; };
  ```

  使用`auto`声明的`derefUPLess`就是一个闭包类型。而使用`std::function`声明的`derefUPLess`是一个`std::function`模板实例，在其内部存储了一个闭包，这样就造成了额外的内存开销和运行时的性能损失（间接调用）。

- 避免类型误用

  ```C++
  // No
  std::vector<int> v;
  unsigned sz = v.size();
  
  // Yes
  std::vector<int> v;
  auto sz = v.size();
  ```
  很少有程序员能够意识到：`v.size()`的真正类型是`std::vector<int>::size_type`。`std::vector<int>::size_type`在32位和64位平台上分别是32位和64位，而`unsigned`在两个平台下都是32位，当程序运行在64位平台上时就会产生问题。
  
  ```C++
  std::unordered_map<std::string, int> m;
  for (const std::pair<std::string, int>& p : m)
  {
      ...
  }
  
  // Yes
  for (const auto& p : m)
  {
      ...
  }
  ```
  
  `std::unordered_map`的键类型应该是`const`的。因此在上面的Hash表中`std::pair`的类型应该是`std::pair<const std::string, int>`，如果写成`std::pair<std::string, int>`，编译器就进行从类型`std::pair<const std::string, int>`到类型`std::pair<std::string, int>`的转换，在转换过程中成产生一个临时对象，从而影响执行效率。

- 抽象的变量类型，更易于编写、理解、维护（重构）

  ```C++
  template<typename It>
  void dwim(It b, It e)
  {  while (b != e) {
        typename std::iterator_traits<It>::value_type
          currValue = *b;
        ...
     }
  }
  
  template<typename It>
  void dwim(It b, It e)
  {  while (b != e) {
        auto currValue = *b;
        ...
     }
  }
  ```

### 要点：

- 相比于使用显示的特定类型，`auto`声明的变量必须被初始化，能够避免类型不匹配导致的移植或效率问题，能够易于重构，并且通常能够减少敲击键盘的次数
- 使用`auto`声明的变量会遭遇在[条款2][Item 2]和[条款6][Item 6]中所述的陷阱

[Item 2]: /《Effective-Modern-C++》学习笔记2
[Item 6]: /《Effective-Modern-C++》学习笔记6
