title: Eclipse + MinGW + Gtest 进行C/C++单元测试
tags: [Eclipse,MinGW,Gtest,C/C++,单元测试]
---

## Step 1： 下载 gtest

最新版本gtest-1.7.0

## Step 2： 解压gtest-1.7.0
解压gtest-1.7.0，打开文件夹gtest-1.7.0。其中"samples"包含了10个gtest的例子；"src"是gtest的源码。 

![]()

## Step 3：在Eclipse中设置gtest的工作环境

### Step 3.1 创建测试工程

打开Eclipse，点击"文件" -> "新" -> C++ Project 
输入"项目名"，例如："unit_test" 

![]()

在"Toolchains:"中选择"MinGW GCC"。 
点击“下一步”，在"Select Configurations"中选择"Debug"，"Release" 

![]()

最后，点击"完成"。"unit_test"将会在左侧的“项目资源管理器”中显示。

### Step 3.2 添加gtest的库，源代码和test文件夹

1. unt_test-> "新" -> "文件夹"，创建源代码(被测代码) 文件夹 “src”。
2. unt_test-> "新" -> "文件夹"，创建头文件文件夹 “include”。
3. unt_test-> "新" -> "文件夹"，创建文件夹 “gtest_src” 存放gtest的库文件。
4. unt_test-> "新" -> "文件夹"，创建文件夹 “test” 存放测试代码。

![]()

### Step 3.3 添加gtest库，被测代码和测试代码
1. 在"gtest_src"文件夹中添加gtest库
   通过执行gtest提供的命令行工具把gtest的库添加到"gtest_src"文件夹中。 

![]()

执行上面的命令之后，工程看起来像这样： 

![]()

2. 添加被测代码

文件夹"include" -> “新” -> Header File，创建头文件"factorial.h"。 

![]()

```C
#ifndef FACTORIAL_H_
#define FACTORIAL_H_
int factorial(int n);
#endif /* FACTORIAL_H_ */
```

下一步，文件夹"src" -> "新" -> Source File，创建"factorial.cpp"源文件。 

![]()

```C
int factorial(int n)
{
    int result = 1;
    for (int i = 1; i <= n; i++)
    {
        result *= i;
    }
    return result;
}
```

3. 添加测试代码

文件夹"test" -> "新" -> Source File，创建"test_factorial.cpp"测试文件。

![]()

```C
# include "gtest/gtest.h"
# include "factorial.h"
 
TEST(IntegerFunctionTest, negative)
{
    EXPECT_EQ(1, factorial(-5));
    EXPECT_EQ(1, factorial(-1));
    EXPECT_GT(factorial(-10), 0);
}
 
TEST(IntegerFunctionTest, DISABLED_zero)
{
    EXPECT_EQ(1, factorial(0));
}
 
TEST(IntegerFunctionTest, postive)
{
    EXPECT_EQ(1, factorial(1));
    EXPECT_EQ(2, factorial(2));
    EXPECT_EQ(6, factorial(3));
    EXPECT_EQ(40320, factorial(8));
}
```

添加"gtest_main.cpp"文件

![]()

```C++
#include <stdio.h>
#include "gtest/gtest.h"
 
GTEST_API_ int main(int argc, char **argv)
{
    printf("Running main() from gtest_main.cc\n");
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

## Step 4：设置运行环境

1. 文件夹"test" -> "属性" -> C/C++ Build -> Setting -> GCC C++ Compiler -> includes -> Include paths (-I) 
点击“add..” -> “workspace..”, 添加文件夹 “include” 和 “gtest_src” 到包含路径。

![]()

2. 文件夹"gtest_src" -> "属性" -> C/C++ Build -> Setting -> GCC C++ Compiler -> includes -> Include paths (-I) 
点击“add..” -> “workspace..”, 添加文件夹“gtest_src” 到包含路径。

![]()

到这里，我们已经设置好了运行环境，就可以开始单元测试了。

## Step 5：运行gtest单元测试

在Eclipse中，点击"项目" -> "全部构建"或者"Ctrl + B"来构建项目。

点击"运行" -> "运行" 或者"Ctrl + F11"来运行可执行文件。 
运行结果如下所示： 

![]()
