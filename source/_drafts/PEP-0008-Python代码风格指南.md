title: PEP 0008 -- Python代码风格指南
tags: [Python, Coding Style]
---

| PEP:          | 8             |
|----------------| ------------- |
| Title:        | Style Guide for Python Code |
| Author:       | Guido van Rossum <guido at python.org>, Barry Warsaw <barry at python.org>, Nick Coghlan <ncoghlan at gmail.com> |
| Status:       | Active |
| Type:         | Process |
| Created:      | 05-Jul-2001 |
| Post-History:	| 05-Jul-2001, 01-Aug-2013 |


## 介绍

本文对`Python`的主要发行版本的标准库的代码给出了编码约定。关于`Python`的`C`实现中的`C`风格指南请见相关的[PEP](#id1)。 

本文和[PEP 257][PEP 257]（`文档字符串约定`）改编于`Guido`的最初的`Python`风格指南文章，并从[Barry的风格指南](#id2)中汲取了一些。

因为语言本身的变化，这一风格指南也随着时间的推移而演变，更多的约定被发现，过时的约定被淘汰。

许多的项目有他们自己的编码风格指南。如果发生了冲突，项目应该居先考虑项目特定的指南。

## 愚蠢的一致性是无知者的心魔

`Guido`的一个核心见解是：**相对于被编写，代码更多的是被阅读。**本指南的目的是改善代码的可读性和使广泛的`Python`代码具有一致性。正如[PEP 20][PEP 20]所说：“可读性应当被重视”。

风格指南关乎一致性。本风格指南所描述的一致性是重要的，而一个项目内部的一致性更重要，一个模块或函数内的一致性最重要。

但最最重要的是：知道什么时候去打破一致性——风格指南并不总是适用的。不能确定时，使用你最好的抉择。看看其它的例子，决定什么看起来最好。并要不耻下问！

特别注意：不要仅仅为了遵守本`PEP`而打破代码的向后兼容性！

出现以下情况时，也是忽略某个指南的好理由：
1. 当应用指南会降低代码的易读性时，即使是对于那些使用本指南来阅读代码的人。
2. 为了和周围的没有遵守本指南的代码（也许是历史原因）保持一致性——虽然这也是一个清除其他人所造成的混乱的时机（真正的`XP`风格）。
3. 由于出现问题的代码早于本指南的引入，并且当前没有其它的原因来修改这一代码。
4. 当代码需要保持和较老版本的`Python`的兼容性，而该版本的`Python`不支持本指针所建议的特性。

## 代码布局

### 缩进

每一个缩进层次使用4个空格。

续行应该和其它元素对齐，要么在垂直方向上使用`Python`的括号内（圆括号、方括号和花括号）的隐式行连接特性进行对齐，要么使用[悬挂式缩进](#id5)。使用悬挂式缩进时应该遵守这些注意事项：第一行不能有参数、应该使用进一步的缩进使续行和其它行区分开来。

符合风格的示例：

```Python
# 在开始分隔符上对齐
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# 使用进一步的缩进使之和其它行区分
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# 悬挂式缩进应该增加一个缩进层次
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

不符合风格的示例：

```Python
# 不适用垂直对齐时，第一行禁止写参数
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# 需要进一步的缩进，因为当前缩进不能区分代码层次
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
```

对于续行，使用4个空格的规则是可选的。

可选的符合风格的示例：

```Python
# Hanging indents *may* be indented to other than 4 spaces.
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

值得注意的是，当<span id="multiline-if-statements">`if`语句</span>的条件部分太长，要求被写在多行的时候，由两个字符的关键字（例如：`if`），加之一个空格和一个开始括号就为多行条件语句的后续行形成了一个天然的4个空格的缩进。这就与`if`语句里面的代码块的缩进（也是自然的缩进4个空格）产生了视觉上的冲突。本`PEP`没有明确的立场关于如何（或是否）采取进一步的措施来把这些条件行和`if`语句的嵌套代码快区分开来。在这种情况下可以接受的选择包括，但不限于：

```Python
# 没有额外的缩进
if (this_is_one_thing and
    that_is_another_thing):
    do_something()

# 添加一个注释，这会在支持语法高亮的编辑器中提供一些区别
if (this_is_one_thing and
    that_is_another_thing):
    # Since both conditions are true, we can frobnicate.
    do_something()

# 在条件续行上添加额外的缩进
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

在多行结构中的结尾花括号/方括号/小括号应该放在多行列的最后一行的第一个非空字符的正下方，例如：

```Python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
```

或者，应该放在多行结构的起始行的第一个字符的正下方，例如：

```Python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```

### 制表符还是空格？

空格是首选的缩进方式。

只有在同已经使用制表符缩进的代码保持一致性的时候才可以使用制表符。

`Python3`不允许混合是由制表符和空格作为缩进。

`Python2`中混合使用制表符和空格做为缩进的代码应该转换为只使用空格。

当使用`-t`选项调用`Python2`命令行解释器时，对混合使用制表符和空格的代码会报出警告。使用`-tt`选项时这些警告会标称错误。强烈建议使用这些选项！

### 单行最大长度

限制所有行的最大长度为79个字节。

对于那些具有很少结构约束的长代码块（文档字符串和注释）来说，单行长度应该限制在72字节。

限制所需要的编辑器窗口的宽度使得并排编辑多个文件成为可能，而且能够使用代码评审工具在相邻的列中呈现两个版本。

在大多数编辑器中使用的默认折叠(wrapping)会扰乱代码的视觉结构，使得代码更难以理解。这里选择的长度限制以避免在那些窗口宽度被设置成80的编辑器中折叠代码，即使这些工具在折叠代码时在最后一列放置了一个图像符号标记。一些基于网络的工具根本就不能提供动态的代码行折叠。

一些团队强烈的倾向于较长的代码行长度。对那些专门或主要的由一个团队维护的代码，可以在这个问题上达成协议，可以把名义上的代码行长度从80增加到100字符（有效地增加最大长度到99字符），前提是注释和文档字符串仍然保存72字符的最大长度。

`Python`标准库是保守的，要求限制行长度为79字符（并且文档字符串/注释为72字符）。

折叠长代码行的首选方式是使用`Python`支持的圆括号、方括号和花括号内部的隐式行延续特性。在括号内，长代码行能够被打断成多行。相比于使用反斜杠进行行延续，这一特性应该优先被使用。

反斜杠有时被可能仍然是适合的。例如，长的，`with`语句不能使用隐式续行，因此反斜杠是可接受的：

```Python
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
```

（见前面有关[多行if语句](#multiline-if-statements)的讨论，对于这样的多行`with`语句需要进一步的思考。）

另一个这样的案例是`assert`语句。

确保后续行有适当的缩进。二元操作符的断行首选位置是在操作符之后，而不是之前。一些例子：

```Python
class Rectangle(Blob):

    def __init__(self, width, height,
                 color='black', emphasis=None, highlight=0):
        if (width == 0 and height == 0 and
                color == 'red' and emphasis == 'strong' or
                highlight > 100):
            raise ValueError("sorry, you lose")
        if width == 0 and height == 0 and (color == 'red' or
                                           emphasis is None):
            raise ValueError("I don't think so -- values are %s, %s" %
                             (width, height))
        Blob.__init__(self, width, height,
                      color, emphasis, highlight)
```

### 空行

用两个空行分割顶层函数和类定义。

使用一个空行分割类中的方法定义。

使用额外的空行（谨慎的）来分割相关的函数组。在一群相关的单行程序（例如，一组哑实现(dummy implementations)）间的空行可以被省略。

谨慎的，在函数内使用空行，来指示逻辑部分。

`Python`接受`Control-L`（即`^L`）换页符作为空格；许多工具把这些字符作为分页符，因此在你的文件中，可以使用它们把相关部分分页。注意，一些编辑器和基于网络的代码浏览器可能不会把`Control-L`识别为换页，并在这个位置显示为另外一个图像符号。

### 源文件编码






[PEP 257]: https://www.python.org/dev/peps/pep-0257

[PEP 20]: https://www.python.org/dev/peps/pep-0020


## 参考

[<span id="id1">1</span>] [PEP 7](https://www.python.org/dev/peps/pep-0007), Style Guide for C Code, van Rossum
[<span id="id2">2</span>] [Barry's GNU Mailman style guide](http://barry.warsaw.us/software/STYLEGUIDE.txt)
[<span id="id3">3</span>] http://www.wikipedia.com/wiki/CamelCase
[4] PEP 8 modernisation, July 2013 http://bugs.python.org/issue18472

脚注

[<span id="id5">5</span>] 悬挂式缩进是一种打字机风格，一个段落中除第一行之外的所有行都缩进。在`Python`中，这一术语用于描述一种风格：一个被括起来的语句的开始括号作为第一行中的最后一个非空字符，其后续行一并缩进，直到遇到结束的括号。 
