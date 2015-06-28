title: 当DP遇见Py(五) -- 工厂方法模式
date: 2014-12-22
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 定义一个用于创建对象的接口，让子类决定实例化哪一个类。这使得一个类的实例化延迟到其子类。

## 类图：
![][1]

## 类型：创建型

<!-- more -->

## 实例：
> 雷锋工厂。学习雷锋好榜样……

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class LeiFeng
{
public:
    void sweep()
    {
        cout<<"扫地"<<endl;
    }
    
    void wash()
    {
        cout<<"洗衣服"<<endl;
    }
}; 

class Student:public LeiFeng
{};

class Volunteer:public LeiFeng
{};

class IFactory
{
public:
    virtual LeiFeng *createLeiFeng()=0;
};

class StuFactory:public IFactory
{
    LeiFeng *createLeiFeng()
    {
        return new Student();
    }
};

class VolFactory:public IFactory
{
    LeiFeng *createLeiFeng()
    {
        return new Volunteer();
    }
};

int main()
{
    IFactory *factory = new VolFactory();
    LeiFeng *xueleifeng = factory->createLeiFeng();
    
    xueleifeng->sweep();
    xueleifeng->wash();
    
    return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class LeiFeng:
    def sweep(self):
        print '扫地'
    def wash(self):
        print '洗衣服'

class Student(LeiFeng):
    pass

class Volunteer(LeiFeng):
    pass

# class IFactory:
#     def createLeiFeng(self):
#         raise NotImplementedError()

# class StuFactory(IFactory):
class StuFactory():
    def createLeiFeng(self):
        return Student()

# class VolFactory(IFactory):
class VolFactory():
    def createLeiFeng(self):
        return Volunteer()


if __name__ == '__main__':
    factory = VolFactory()
    xueleifeng = factory.createLeiFeng()

    xueleifeng.sweep()
    xueleifeng.wash()
```

## Tips:

`Python`实现的工厂方法模式相比与`C++`来说并没有什么显著特点，因此我不在对工厂方法做过多论述。感兴趣的同学可以参照《当DP遇见Py--简单工厂模式》使用简单工厂来实现该需求，然后对比一下`工厂方法模式`和`简单工厂模式`之间的优缺点。

仔细观察上面`Python`代码的注释部分，为什么我们不需要`IFactory`这个类呢？？？
要回答这个问题我们需要了解`Python`的**多态**。我们知道多态是面向对象的一大特征，来完成运行期的动态绑定。其实Python并不支持传统意义上的多态，它使用“鸭子类型”来模拟多态。我们下面着重看一下“鸭子类型”。

### 鸭子类型
鸭子类型（duck typing）是动态类型的一种风格。
在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由当前方法和属性的集合决定。
这个概念的名字来源于由 James Whitcomb Riley 提出的鸭子测试，“鸭子测试”可以这样表述：

> 当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。

在鸭子类型中，关注的不是对象的类型本身，而是它是如何使用的。
例如，在不使用鸭子类型的语言中，我们可以编写一个函数，它接受一个类型为鸭子的对象，并调用它的走和叫方法。在使用鸭子类型的语言中，这样的一个函数可以接受一个任意类型的对象，并调用它的走和叫方法。如果这些需要被调用的方法不存在，那么将引发一个运行时错误。任何拥有这样的正确的走和叫方法的对象都可被函数接受的这种行为引出了以上表述，这种决定类型的方式因此得名。
鸭子类型通常得益于不测试方法和函数中参数的类型，而是依赖文档、清晰的代码和测试来确保正确使用。

再来看一下代码，`StuFactory`和`VolFactory`不需要继承`IFactory`，它们只需要实现`createLeiFeng()`这个方法就好了。任何实现了`createLeiFeng()`这个方法的对象，都可以被称作`工厂鸭`。(*^__^*) 

![][2]    你丫是在说我么？


[1]: http://78rbbi.com1.z0.glb.clouddn.com/工厂方法模式.png
[2]: http://img4.duitang.com/uploads/blog/201401/04/20140104141143_R4rJx.gif
