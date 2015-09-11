title: PEP 0008 -- Python代码风格指南
toc: true
mathjax: false
tags:
  - Python
  - Coding Style
  - 翻译
date: 2015-09-11 22:50:21
categories: Python
---

译自：https://www.python.org/dev/peps/pep-0008/


| PEP:          | 8             |
|----------------| ------------- |
| Title:        | Style Guide for Python Code |
| Author:       | Guido van Rossum <guido at python.org>, Barry Warsaw <barry at python.org>, Nick Coghlan <ncoghlan at gmail.com> |
| Status:       | Active |
| Type:         | Process |
| Created:      | 05-Jul-2001 |
| Post-History:	| 05-Jul-2001, 01-Aug-2013 |


## 介绍

本文对`Python`的主要发行版本的标准库的代码给出了编码约定。关于`Python`的`C`实现中的`C`风格指南请见相关的PEP[[1]](#id1)。 

本文和[PEP 257][PEP 257]（`文档字符串约定`）改编于`Guido`的最初的`Python`风格指南文章，并从Barry的风格指南中汲取了一些[[2]](#id2)。

因为语言本身的变化，这一风格指南也随着时间的推移而演变，更多的约定被发现，过时的约定被淘汰。

许多的项目有他们自己的编码风格指南。如果发生了冲突，项目应该居先考虑项目特定的指南。

<!--more-->

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

符合规范的示例：

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

不符合规范的示例：

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

可选的符合规范的示例：

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

`Python`标准库采用保守策略，限制行长度为79字符（并且文档字符串/注释为72字符）。

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

`Python`发布的核心代码应该总是`UTF-8`的（或者在`Python2`中是`ASCII`的）。
使用`ASCII`（在`Python2`中）或`UTF-8`（在`Python3`中）编码的文件不应该有编码声明(encoding declaration)。

在标准库中，非默认编码应该仅用于测试或当注释或文档字符串需要提及作者的名字，而该名字包含非`ASCII`字符时；否则，使用`\x`，`\u`，`\U`或`\N`等转义符在字符串字面值中包含非`ASCII`数据是首选的方式。

对于`Python3.0`和以后版本，在标准库中使用了以下策略（见[PEP 3131][PEP 3131]）：所有的标识符**必须**是`ASCII`标识符，只要切实可行都**应该**使用英语单词（在许多情况下，使用的缩写和技术术语不是英语单词）。此外，字符串字面值和注释必须也是`ASCII`的。唯一的例外是：(a)测试非`ASCII`特性的测试用例，和(b)作者的名字。那些不是基于拉丁字母的作者名字**必须**提供一个拉丁英译。

推荐那些具有全球视野的开源项目采用类似的策略。

### 导入

- 导入通常应该在单独的行中，例如：
  符合规范的示例：
  ```Python
  import os
  import sys
  ```

  不符合规范的示例：
  ```Python
  import sys, os
  ```

  不过这样的导入也是可以的：
  ```Python
  from subprocess import Popen, PIPE
  ```
- 导入应该总是放在文件的顶部，在模块的注释和文档字符串之后，在模块全局变量和常量之前。

  导入应该使用下面的顺序进行分组：
  1. 导入标准库
  2. 导入相关的第三方库
  3. 导入相关的本地应用程序/库

  你应该在每组导入之间放置一个空行。

  任何相关的`__all__`规范放在导入之后。

- 推荐使用绝对路径导入，因为它们通常会更可读，而且在导入系统没有被正确配置的情况下（例如，一个包内的目录恰好在`sys.path`中）具有更好的表现（或至少能够给出更好的错误信息）:
  ```Python
  import mypkg.sibling
  from mypkg import sibling
  from mypkg.sibling import example
  ```

  然而，在有些时候，显示的相对导入是绝对导入可接受的替代，特别是在应对复杂的包结构时绝对导入会出现不必要的繁琐的情况下：
  ```Python
  from . import sibling
  from .sibling import example
  ```

  标准库的代码应该避免复杂的包结构，因此总是使用绝对导入。

  不应该使用隐式的相对导入，并且`Python3`已经去掉了对它的支持。

- 当从一个包含类的模块中导入类时，这样写通常是合理的：
  ```Python
  from myclass import MyClass
  from foo.bar.yourclass import YourClass
  ```

  若果这样写会引起本地名字冲突，就使用下面的方式：
  ```Python
  import myclass
  import foo.bar.yourclass
  ```

  并且使用"myclass.MyClass"和"foo.bar.yourclass.YourClass"这样的方式来引用。

- 通配符导入（`from <module> import *`）应该被禁止。因为，这导致哪些名字存在命名空间中变得不清晰，迷惑读者和许多自动化工具。不过，有一个能站得住脚的使用通配符导入的案例：为了重新发布一个内部的接口作为公共`API`的一部分（例如，使用纯`Python`来重写一个可选的加速模块定义的接口，但你不能事先确定到底哪些接口会被重写）。

  当以这样的方式重新发布命名时，还应该遵守本指针有关公有的和内部的接口的规范。

## 字符串的引号

在`Python`中，单引号字符串和双引号字符串是相同的。本`PEP`并不对其做出建议，选择一个规则，坚持下去就可以了。然而，当字符串包含单引号或双引号时，应该使用另一个引号来避免在字符串中使用反斜杠。这会改善可读性。

对于三个引号的字符串，总是使用双引号与在[PEP 257][PEP 257]所约定的文档字符串规范保持一致。

## 表达式和语句中的空格

### 通病

在以下的情况种避免额外的空格：

- 紧接着圆括号、方括号或花括号内

  Yes:
  ```Python
  spam(ham[1], {eggs: 2})
  ```
  No:
  ```Python
  spam( ham[ 1 ], { eggs: 2 } )
  ```

- 逗号、分号或冒号之前：

  Yes:
  ```Python
  if x == 4: print x, y; x, y = y, x
  ```
  No:
  ```Python
  if x == 4 : print x , y ; x , y = y , x
  ```

- 然而，在slice（切片）中，冒号就像一个二元操作符，并且在它的两侧应该有相等数量的操作数（把它当做最低优先级的操作符）。在扩展slice中，两个冒号必须具有相等数量的空格。例外：当slice的某个参数省略了，那么其相应的空格也应该省略。

  Yes:
  ```Python
  ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
  ham[lower:upper], ham[lower:upper:], ham[lower::step]
  ham[lower+offset : upper+offset]
  ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
  ham[lower + offset : upper + offset]
  ```
  No:
  ```Python
  ham[lower + offset:upper + offset]
  ham[1: 9], ham[1 :9], ham[1:9 :3]
  ham[lower : : upper]
  ham[ : upper]
  ```

- 函数调用参数列表的开始括号之前:

  Yes:
  ```Python
  spam(1)
  ```
  No:
  ```Python
  spam (1)
  ```

- 索引或切片的开始括号之前：

  Yes:
  ```Python
  dct['key'] = lst[index]
  ```
  No:
  ```Python
  dct ['key'] = lst [index]
  ```

- 在赋值（或其它）操作符两侧，多余一个的，用于和其它操作符对齐为目的空格：

  Yes:
  ```Python
  x = 1
  y = 2
  long_variable = 3
  ```
  No:
  ```Python
  x             = 1
  y             = 2
  long_variable = 3
  ```

### 其它建议

- 总是在这些二元操作符两侧使用一个空格：赋值（=），参数赋值（+=、-= 等等），比较（==、<、>、!=、<>、<=、>=、in、not in、is、is not），布尔（and、or、not）。

- 如果使用不同优先级的操作符，考虑在最低优先级的操作符两侧加上空格。请自行判断，无论怎样，绝不要使用多余一个空格，并且使得二元操作符两侧具有相同数量的空格。

  Yes:
  ```Python
  i = i + 1
  submitted += 1
  x = x*2 - 1
  hypot2 = x*x + y*y
  c = (a+b) * (a-b)
  ```
  No:
  ```Python
  i=i+1
  submitted +=1
  x = x * 2 - 1
  hypot2 = x * x + y * y
  c = (a + b) * (a - b)
  ```

- 在关键字参数或默认参数中的`=`号两侧不要出现空格。

  Yes:
  ```Python
  def complex(real, imag=0.0):
      return magic(r=real, i=imag)
  ```
  No:
  ```Python
  def complex(real, imag = 0.0):
      return magic(r = real, i = imag)
  ```

- 在带注解的函数定义中的`=`号两侧使用空格。此外，在`:`号之后使用一个空格，以及在`->`号两侧各使用一个空格，代码一个注解的返回值。

  Yes:
  ```Python
  def munge(input: AnyStr):
  def munge(sep: AnyStr = None):
  def munge() -> AnyStr:
  def munge(input: AnyStr, sep: AnyStr = None, limit=1000):
  ```
  No:
  ```Python
  def munge(input: AnyStr=None):
  def munge(input:AnyStr):
  def munge(input: AnyStr)->PosInt:
  ```

- 不建议使用符合语句（同一行中有多条语句）。

  Yes:
  ```Python
  if foo == 'blah':
      do_blah_thing()
  do_one()
  do_two()
  do_three()
  ```
  No:
  ```Python
  if foo == 'blah': do_blah_thing()
  do_one(); do_two(); do_three()
  ```

- 虽然有时把较短的`if/for/while`语句放在一行也是可以的，但决不能把它运用到多字句的语句中。这样也是为了避免折叠长行。

  No:
  ```Python
  if foo == 'blah': do_blah_thing()
  for x in lst: total += x
  while t < 10: t = delay()
  ```
  No:
  ```Python
  if foo == 'blah': do_blah_thing()
  else: do_non_blah_thing()
  
  try: something()
  finally: cleanup()
  
  do_one(); do_two(); do_three(long, argument,
                               list, like, this)

  if foo == 'blah': one(); two(); three()
  ```

## 注释

与代码相矛盾的注释，还不如没有注释。总是保持注释随着代码更新！

注释应该是完整的句子。如果注释是一个短语或句子，它的第一个单词的首字母应该大写，除非第一个单词是一个以小写字母开头的标识符（绝不要改变标识符的大小写！）。

如果一个注释很短，结尾的句点就可以省略。块注释通常由完整的句子建立起的多个段落组成，并且每个句子都应该以句点结束。

你应该在句子结束的句点后面使用两个空格。

当撰写英文注释时，参考《The Elements of Style》(Strunk and White)。

来自于非英语国家的`Python`程序员：请使用英语写你的注释，除非你120%的确定你写的代码绝不会被不懂得你的语言的人阅读。


### 块注释

块注释通常都是用来阐述跟随其后的代码的，并和该代码具有相同的缩进层次。块注释的每一行都以`#`和一个空格开始（除非在注释内部有文本缩进）。

块注释中的段落都以只包含一个`#`的行隔开。

### 行内注释

尽量少的使用行内注释。

行内注释就是同语句在同一行内的注释，应该使用至少两个空格同语句隔开，以一个`#`和空格开始。

如果行内注释表述的含义及其明显，那么它就是多余的，而且还会分散读者的注意力。不要撰写这样的注释：

```Python
x = x + 1                 # Increment x
```

但有些时候，行内注释也是有用的：

```Python
x = x + 1                 # Compensate for border
```

### 文档字符串

关于编写好的文档字符串（亦称 "docstrings"）的约定请参见[PEP 257][PEP 257]。

- 为所有的共有的模块、函数、类和方法撰写文档字符串。对于非公有方法，文档字符串是没有必要的，但你应该使用注释来描述该方法的用途，并且注释应该在`def`行之后。

- [PEP 257][PEP 257] 描述了好的文档字符串的约定。注意，最重要的是，多行文档字符串结尾的`"""`应该单独放在一行，例如：

  ```Python
  """Return a foobang

  Optional plotz says to frobnicate the bizbaz first.
  """
  ```

- 对于单行的文档字符串，请保持结束的`"""`在同一行上。

## 版本注记

如果在你的源文件中有`Subversion`、`CVS`或`RCS`等版本更新记录，像下面这样写：

```Python
__version__ = "$Revision$"
# $Source$
```

以上这些内容应该放在模块的文档字符串后面，代码之前，以一个空行分割其前后。

## 命名约定

`Python`库中的命名约定有些混乱，我们永远不能使之完全一致——不过，本指南里的是当前推荐的命名标准。新的模块和包（包括第三方框架）应该使用该标准编写，但如果一个以存在的库有不同的分割时，保持内部一致性是首选。

### 最重要的原则

作为公共`API`的一部分，用户可见的名字应该遵守这一约定：名字反映功能而不是实现。

### 描述：命名风格

有许多中不同的命名风格。独立于它们的用途，识别使用了什么命名风格是有帮助的。

下面是一些著名的命名风格：
- b（单一小写字母）
- B（单一大写字母）
- lowercase
- lower_case_with_underscores
- UPPERCASE
- UPPER_CASE_WITH_UNDERSCORES
- CapitalizedWords(or CapWords, or CamelCase -- 命名得自于其字母崎岖的形状[[3]](#id3)).
  有时也被称作`StudlyCaps`。
  注意：在`CapWords`中使用缩写时，缩写都使用大写字母。因此`HTTPServerError`好于`HttpServerError`。
- mixedCase(与`CapitalizedWords`不同的是，其首字母小写！)
- Capitalized_Words_With_Underscores (丑陋的！)

还有的风格是使用一个简短的唯一的前缀来把相关的名字分组。这种风格在`Python`中使用的不多，但为了完整性还是提及一下。例如，`os.stat()`函数返回一个元组(tuple)，其成员传统上的命名方式如同`st_mode`、`st_size`、`st_mtime`等等。（这样做是为了强调其对应于`POSIX`系统调用结构体的成员，这有助于程序员熟悉其使用方式。）

`X11`库的所有共有函数都使用一个`X`作为开始。在`Python`中，这一风格被认为是不必要的，因为属性和方法名都以对象为前缀，而函数名以模块名为前缀。

此外，在下面的特殊形式中使用下划线作为前缀或后缀是被公认的（它们通常与以上形式的风格一起使用）：
- `_single_leading_underscore`：弱“内部使用”指示。例如，`from M import *`不会导入以一个下划线开始的名字。
- `single_trailing_underscore_`：约定使用这样的方式来避免和`Python`关键字冲突，例如：
  ```Python
  Tkinter.Toplevel(master, class_='ClassName')
  ```
- `__double_leading_underscore`：当用于命名一个类的属性时，会出发名字篡改(`name mangling`)机制（在类`FooBar`内，`__boo`变成`_FooBar__boo`）
- `__double_leading_and_trailing_underscore__`：驻留在用户可以控制的命名空间内的“魔法”对象或属性。例如，`__init__`、`__import__`或`__file__`。不要创造这样的名字，只使用它们。

### 规定：命名约定

#### 避免使用的命名

不要使用字符`'l'`(小写字母`el`)，`'O'`（大写字母`oh`）或`'I'`（大写字母`eye`）作为单字符变量名。

在一些字体里，这些字符和数字1和0难以区分。需要临时使用`'l'`的时候，使用`'L'`代替。

#### 包和模块的命名

模块应该使用简短的、都小写的名字。在模块名中可以使用下划线，如果它可以提高可读性的话。`Python`包也应该使用简短的、都小写的名字，然而，并不提倡使用下划线。

因为模块名映射为文件名，加之一些文件系统不区分大小写且会截断长的文件名，因此为模块名选择相当短的名字是很重要的——这在`Unix`系统中并不存在问题，但当把代码搬运到老版本的`Mac`或`Windows`或`DOS`系统时就会出现问题。

当一个以`C`或`C++`编写的扩展模块有一个伴随的，提供高层接口的`Python`模块时，`C/C++`模块需要以一个下划线开始（例如，`_socket`）。

#### 类命名

正常情况下，类名使用`CapWords`风格。

The naming convention for functions may be used instead in cases where the interface is documented and used primarily as a callable.

注意，对于内建(builtin)名字有一个单独的约定：大多数的内建名字都是单个单词（或两个单词和在一起），`CapWords`约定仅使用在异常命名和内建常量上。

#### 异常命名

因为异常应该是类，因此使用类命名的约定。然而，你应该在你的异常命名上使用`"Error"`后缀（如果异常确实是一个错误）。

#### 全局变量命名

（让我们期望这些变量仅在一个模块内部使用。）它的命名约定和函数的命名约定差不多。

对那些设计能够使用`from M import *`的模块应该使用`__all__`机制来阻止导出全局变量，或者使用前面的约定在全局变量上加上下划线前缀（通过这样表示你想要使这些全局变量成为“模块非公有”）。

#### 函数命名

函数命名应该使用`lowecase`风格，根据需要以下划线分割单词来提高可读性。

`mixedCase`风格仅在那些已经大量使用该风格的上下文中使用（例如，`threading.py`)，为了保持向后兼容性。

#### 函数和方法参数

总是使用`self`作为实例方法的第一个参数。

总是使用`cls`作为类方法的第一个参数。

如果函数的参数名和一个保留关键字冲突，通常的做法是在名字后面加上一个下划线，而不是使用缩写或拼写贪墨(spelling corruption)。因此`class_`好于`clss`。（或许通过使用同义词来避免冲突会更好。）

#### 方法名和实例变量

使用函数命名规则：`lowercase`风格，根据需要使用下划线分割单词以提高可读性。

为非公有的方法和实例变量添加一个下划线前缀。

避免名字和子类冲突，使用两下划线前缀来触发`Python`的名字篡改机制。

`Python`会使用类名来篡改这些名字：如果类`Foo`有一个属性为`__a`，它就不能够使用`Foo.__a`来访问。（一个坚持的用户任然可以通过调用`Foo._Foo__a`来获得访问。）通常，双下划线前缀仅用于避免基类的属性和它的子类的属性相冲突。

注意：有关使用 `__` 的名字还有些争论。

#### 常量

常量通常定义在模块的层级上，所有字母全大写，使用下划线分割单词。例子包括`MAX_OVERFLOW`和`TOTAL`。

#### 继承设计

总是要判断一个类的方法和实例变量（统称：“属性”）是否是公有的或非公有的。如果存在疑虑，先选择非公有；因为相比于把公有属性改为非公有，把非公有改为公有更容易。

公共属性是指那些你希望你的类客户所使用的，并且你承诺过不会有不向后兼容的改变的属性。非公有属性是指那些不打算被第三方使用的，你不能保证不去修改或甚至被删除的属性。

我们不在这使用“私有”这一术语，因为在`Python`中并没有真正的私有属性（without a generally unnecessary amount of work）。

另一类属性是“子类API”（在其它语言中通常称为"protected"）。一些类设计为基类，可以扩展或修改其行为。当设计这样的类时，要小心地做出明确决定：哪些属性是公有的、哪些作为子类`API`、哪些确实只在你的基类中使用。

据此，以下是`Python`化(Pythonic)的指南：

- 公有属性不要下划线前缀

- 如果公有属性名和保留关键字相冲突，在你的属性名末尾加上一个下划线。（然而，尽管有这条规则，`'cls'`仍然是表示一个类变量或参数的首选方式，特别是作为类方法的第一个参数。）

  注意1：见以上关于类方法的参数名建议。

- 对于简单的公有数据属性，最好是直接暴露属性名，不要使用复杂的访问器/修改器方法。记住`Python`为你提供了在未来增强属性的简单方式，高了摸个时候你会发现一个简单的数据属性需要增加功能行为。在这种情况下，使用属性(property)把功能实现隐藏在简单的数据属性(data attribute)访问语法之后。

  注意1：属性(property)只在新型类中工作。
  注意2：尽量保持功能属性没有副作用，虽然如缓存(caching)这样的副作用一般都没有问题。
  注意3：避免使用属性来进行计算昂贵的操作；这种属性记法会是的调用者相信这一访问是（相对）廉价的。

- 如果你的类的意图是为了被子类化，并且该类中有你不想要子类使用的属性，考虑使用双下划线前缀和没有下划线后缀的方式来命名这些属性。这会触发`Python` 名字篡改算法，把类名加入到(mangled into)属性名中。这样会避免子类无意中包含了和基类同名的属性而引起的名字冲突。

  注意1：请注意在篡改名字时仅简单的使用了类名，因此如果你的子类同时使用了与基类相同的类名和属性名，仍然会出现命名冲突。
  注意2：名字篡改使得某些使用者，如调试和`__getattr__()`，变得不方便。然而，名字篡改算法还是有据可查和易于手动地执行的。
  注意3：并不是每个人都喜欢名字篡改。试着去平衡避免名字冲突的需要和将来潜在的调用需求。

### 公共的和内部的接口

任何向后兼容性保证只适用于公共接口。相应的，能够使用户清晰的区分公共的和内部的接口是重要的。

文档化的接口被认为是公共的，除非在文档中明确的声明接口是临时的或内部的，以免除通常的向后兼容性保证。所有未文档化的接口都被假定为内部的。

为了更好地支持自省(introspection)，模块应该使用`__all__`属性来显示的声明它的所提供的公共的名字。设置`__all__`为一个空的`list`，表示该模块没有公共的`API`。

即使适当的设置了`__all__`，内部的接口（包、模块、类、函数、属性或其它名字）应该仍要使用一个下划线作为其前缀。

在任何内部的命名空间（包、模块或类）中的接口也被认为是内部的。

应该总是认为被导入的名字是一个实现的细节。其它的模块绝不能对这些被导入的名字进行间接访问，除非它们是作为导入它们的模块的，显示文档化了的`API`的一部分，诸如`os.path`或一个包的`__init__`模块（用于从子模块公开功能）。

## 编程建议

- 代码不要编写的不利于其它的`Python`实现（`PyPy`、`Jython`、`IronPython`、`Cython`、`Psyco`等等）。

  例如，不要依赖`CPython`的，形如`a += b`或`a = a + b`这样的语句，高效的就地字符串连接现实。这一优化即使是在`CPython`中也很脆弱（它只能用于某些类型），并且在那些没有使用引用计数(refcounting)的实现中根本就不存在。对于性能比较敏感的库中，应该使用`''.join()`的形式。它会确保在不同的实现中连接都会在线性时间内。

- 同`None`比较时，总是使用`is`或`is not`，而不是相等操作符。

  同样，谨防当你的真实意图是`if x is not None`而写成`if x` —— 例如，在测试一个变量或参数是否默认为`None`，从而给它赋其它值的时候。而这个其它值（例如容器）在布尔上下文中可能是`false`！

- 使用`is not`操作符而不是`not ... is`。虽然两个表达式功能性相同，但前者的可读性更强。

  Yes:
  ```Python
  if foo is not None:
  ```
  No:
  ```Python
  if not foo is None:
  ```

- 当需要丰富的比较来实现排序操作时，最好实现所有六个操作符(`__eq__`,`__ne__`,`__lt__`,`__le__`,`__gt__`,`__ge__`)，而不是依赖其它的代码来使用一个特定的比较操作符。

  为了简化，`functools.total_ordering()`装饰器提供了一个工具来生成缺失的比较方法。

  根据[PEP 207][PEP 207]的指示，`Python`使用自反性规则。也就是，解释器能够交换`y > x`和`x < y`，`x == y`和`x != y`。`sort()`和`min()`操作保证使用`<`操作符，而`max()`函数使用`>`操作符。然而，最好还是实现所有的六个操作符，使得在其它的场合不会引起混乱。

- 总是使用`def`语句，而不是使用赋值语句直接把一个`lambda`表达式绑定到一个标识符。

  Yes:
  ```Python
  def f(x): return 2*x
  ```
  No:
  ```Python
  f = lambda x: 2*x
  ```

  第一中形式意味着该函数对象的确切的名字是`'f'`，而不是通用的`'<lambda>'`。这更利于`traceback`和通用的字符串表示。赋值语句的使用消除了`lambda`表达式相比于显示的`def`语句可以提供的唯一好处（例如，它可以被嵌入更大的表达式）。

- 从`Exception`而不是`BaseException`派生异常。直接从`BaseException`继承保留给那些不需要捕捉的异常（捕捉它们几乎总是错误的）。

  设计异常层次结构应该基于是为了什么需要来捕获异常的区分，而不是所抛出异常的位置。宗旨是以编程的方式回答“出现了什么问题？”，而不是仅仅说明“一个问题发生了”（见[PEP 3151][PEP 3151]，从内置异常层次结构学习到的一个示例）。

  在这适用于类命名规范，然而当异常是一个错误(error)时，你应该给它加上`"Error"`后缀。被用于非局部流程控制或其它形式的信号的非错误异常不需要特殊的后缀。

- 适当的使用异常链。在`Python 3`中，应该使用`"raise X from Y"`，这样就不会导致原有的`traceback`丢失。

  当故意的取代内部异常的时候（在`Python 2`中使用`"raise X"`，在`Python 3.3+`中使用`"raise X from None"`），要确保相关的细节被转移到了新的异常里了（例如，在把`KeyError`转换到`AttributeError`时保留其属性名，或把原异常的文本嵌入到新异常的消息中）。

- 在`Python 2`中抛出异常的时候，使用`raise ValueError('message')`的形式，而不是`raise ValueError, 'mdssage`这种过时的形式。

  在`Python 3`中，后一种形式不符合语法。

  使用括号的形式也意味着当异常的参数太长或包含字符串格式化时，得益于所包含的括号，你不需要使用行延续字符。

- 当捕获异常时，尽量提到具体的异常，而不要仅使用裸的`except:`字句。

  例如，使用：

  ```Python
  try:
      import platform_specific_module
  except ImportError:
      platform_specific_module = None
  ```

  裸的`except:`字句会捕获`SystemExit`和`KeyboardInterrupt`异常，导致不能使用`Control-C`来中断一个程序，而且还会掩盖其它问题。如果你要捕获所有标志程序错误的异常，请使用`except Exception：`（裸的`except:`等价于`except BaseException:`）。

  一种比较好的做法是只在以下两种情况中使用裸的`'except'`字句：
  1. 如果异常处理程序会打印或日志记录`traceback`；至少用户将会意识到发生了一个错误。
  2. 如果需要代码做一些清理工作，然后使用`raise`把异常向上传播。`try ... finally`可能是一个更好的方式来处理这种情况。

- 当绑定被捕获的异常到一个名字时，使用在`Python 2.6`中添加的显示名字绑定语法：

  ```Python
  try:
      process_data()
  except Exception as exc:
      raise DataProcessingFailedError(str(exc))
  ```

  这是`Python 3`中唯一支持的语法，并且能够避免老式基于逗号的语法所带来的模棱两可。

- 当捕获操作系统错误时，宁愿使用`Python 3.3`引入的显示异常j层次结构，而不是对`errno`值做自省。

- 此外，对于所有的`try/except`字句，限制`try`字句的语句数量到最小是有必要的。再一次，这样做避免了掩盖`bug`。

  Yes:
  ```Python
  try:
      value = collection[key]
  except KeyError:
      return key_not_found(key)
  else:
      return handle_value(value)
  ```
  No:
  ```Python
  try:
      # Too broad!
      return handle_value(collection[key])
  except KeyError:
      # Will also catch KeyError raised by handle_value()
      return key_not_found(key)
  ```

- 当一个资源只在一个特定代码片段中使用时，使用`with`语句来确保：当该资源被使用完后，会被及时地和可靠地清理。使用`try/finally`语句也是可以接受的。

- 上下文管理(context manager)都应该通过单独的函数或方法来调用，无论它们会不会做除了获取和释放资源以外的事情。例如：

  Yes:
  ```Python
  with conn.begin_transaction():
      do_stuff_in_transaction(conn)
  ```
  No:
  ```Python
  with conn:
      do_stuff_in_transaction(conn)
  ```

  后一个例子并不能提供任何的信息，表明`__enter__`和`__exit__`方法会做除了在一个事务(transaction)结束之后关闭连接之外的其它任何事情。在这种情况下，显示(explicit)是很重要的。

- 在`return`语句中保持一致。在一个函数中的所有`return`语句，要么都返回一个表达式，要么都不返回。如果有任何一个`return`语句返回一个表达式，那么其它任何不返回值的`return`语句都要显示的使用`None`作为返回值，并且在函数的最后应该放一个显示的`return`语句（如果可以到达的话）。

  Yes:
  ```Python
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
      else:
          return None
  
  def bar(x):
      if x < 0:
          return None
      return math.sqrt(x)
  ```
  No:
  ```Python
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
  
  def bar(x):
      if x < 0:
          return
      return math.sqrt(x)
  ```

- 使用字符串的方法而不是字符串模块。

  字符串方法总是比较快，而且和`Unicode`字符串具有相同的`API`。如果为了同`Python 2.0`和其以前的版本保持后向兼容，请忽略本条款。

- 使用`".startswith()"`和`".endswith()"`来检查前缀和后缀，而不是字符串切片(slicing)。

  `startswith()`和`endswith()`更简洁，不容易出错。例如：

  Yes:
  ```Python
  if foo.startswith('bar'):
  ```
  No:
  ```Python
  if foo[:3] == 'bar':
  ```

- 应该总是使用`ininstance()`进行对象类型比较，而不是直接比较类型。

  Yes:
  ```Python
  if isinstance(obj, int):
  ```
  No:
  ```Python
  if type(obj) is type(1):
  ```

  当检查一个对象是否时字符串时，请记住：它有可能是一个`Unicode`字符串！在`Python 2`中，`str`和`unicode`具有相同的基类，`basestring`，因此，你可以这样做：

  ```Python
  if isinstance(obj, basestring):
  ```

  注意，在`Python 3`中已经不存在`unicode`和`basestring`了（只有`str`了），并且`bytes`对象已经不在是一种字符串了（它变成了一个整数序列）。

- 对于序列（`string`、`list`、`tuple`），运用空序列是`false`这一事实。

  Yes:
  ```Python
  if not seq:
  if seq:
  ```
  No:
  ```Python
  if len(seq)
  if not len(seq)
  ```

- 不要写出依赖于以有意义的空格结尾的字符串字面值。这样的空格在视觉上无法区分，并且有些编辑器会修剪它们。

- 不要使用`==`来进行布尔值和`True`或`False`的比较。

  Yes:
  ```Python
  if greeting:
  ```
  No:
  ```Python
  if greeting == True:
  ```
  Worse:
  ```Python
  if greeting is True:
  ```

- `Python`标准库不会使用函数注解，因为这会导致对一个特定的注解风格过早的承诺。反之，留给用户来发现和实验有用的注解风格。

  *注解部分省略*

## 参考

[<span id="id1">1</span>] [PEP 7](https://www.python.org/dev/peps/pep-0007), Style Guide for C Code, van Rossum
[<span id="id2">2</span>] [Barry's GNU Mailman style guide](http://barry.warsaw.us/software/STYLEGUIDE.txt)
[<span id="id3">3</span>] http://www.wikipedia.com/wiki/CamelCase
[4] PEP 8 modernisation, July 2013 http://bugs.python.org/issue18472

脚注

[<span id="id5">5</span>] 悬挂式缩进是一种打字机风格，一个段落中除第一行之外的所有行都缩进。在`Python`中，这一术语用于描述一种风格：一个被括起来的语句的开始括号作为第一行中的最后一个非空字符，其后续行一并缩进，直到遇到结束的括号。 


[PEP 20]: https://www.python.org/dev/peps/pep-0020
[PEP 207]: https://www.python.org/dev/peps/pep-207
[PEP 257]: https://www.python.org/dev/peps/pep-0257
[PEP 3131]: https://www.python.org/dev/peps/pep-3131
[PEP 3151]: https://www.python.org/dev/peps/pep-3151

