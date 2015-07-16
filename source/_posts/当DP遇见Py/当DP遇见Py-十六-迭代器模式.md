title: 当DP遇见Py(十六)--迭代器模式
toc: false
mathjax: false
date: 2015-07-13 09:40:37
categories: Python
tags: [Python, C++, DP]
---

## 定义：

> 提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象中的内部表示。

## 类图：
![此处输入图片的描述][1]

## 类型：结构型

## 实例：

> 乘车买票

<!-- more -->

### C++ 实现

```C++
#include <iostream>
#include <string>
#include <list>

using namespace std;

class Iterator;
class Aggregate;
class BusAggregate;

typedef string Passenger ;

class Iterator
{
public:
    virtual Passenger first()=0;
    virtual Passenger next()=0;
    virtual bool hasNext()=0;
};

class BusIterator:public Iterator
{
public:
    BusIterator(BusAggregate* bus)
        :aggreate(bus), current(0)
    {
    }
    
    Passenger first();
    Passenger next();
    bool hasNext();
    
private:
    BusAggregate* aggreate;
    int current;
};

class Aggregate
{
public:
	virtual Iterator *createIterator()=0;
};

class BusAggregate: public Aggregate
{
public:
    friend BusIterator;
    
    BusAggregate()
    {
        count = 0;
    }
    Iterator *createIterator()
    {
        return new BusIterator(this);
    }
    
    void getOn(Passenger passenger)
    {
        sites[count++] = passenger;
    }

private:
    Passenger sites[37];
    int count;
};

Passenger BusIterator::first()
{
    current = 0;
    return next();
}
    
Passenger BusIterator::next()
{
    return aggreate->sites[current++]; 
}
    
bool BusIterator::hasNext()
{
    if(current == aggreate->count + 1)
    {
        return false;
    }
    else
    {
        return true;
    }
}

int main()
{
    BusAggregate bus;
    bus.getOn("大鸟");
    bus.getOn("小菜");
    bus.getOn("行李");
    bus.getOn("老外");
    bus.getOn("公交内部员工");
    bus.getOn("小偷");
    
    Iterator *iter = bus.createIterator();
    
    Passenger p = iter->first(); 
    cout << p << " 请买车票！" << endl;
    while(iter->hasNext())
    {
        cout << p << " 请买车票！" << endl;
        p = iter->next();
    }
    
}
```

### Python 实现

```python
# -*- coding=utf-8 -*-

class Bus():
    sites = 37

    def __init__(self):
        self.passenger = []

    def getOn(self, passenger):
        if len(self.passenger) > self.sites:
            raise "has no site!"
        self.passenger.append(passenger)

    def __iter__(self):
        for p in self.passenger:
            yield p

if __name__ == '__main__':
    bus = Bus()
    bus.getOn("大鸟")
    bus.getOn("小菜")
    bus.getOn("行李")
    bus.getOn("老外")
    bus.getOn("公交内部员工")
    bus.getOn("小偷")

    for p in bus:
        print '{} 请买车票！'.format(p)
```

### 执行结果：

```python
大鸟 请买车票！
小菜 请买车票！
行李 请买车票！
老外 请买车票！
公交内部员工 请买车票！
小偷 请买车票！
```

## Tips:

在`Python`中像`list`、`tuple`、`map`等内置类型天生的支持迭代。自己定义迭代器也很简单，只要是实现了`__iter__`接口的对象，就可以使用迭代器进行访问。如上所示，在`bus`类中定义了`__iter__`方法，返回e一个迭代器。

- `__iter__` 是实现迭代器的接口，是`Python`的魔术方法
- `yield` 产生生成器函数。简而言之，包含`yield`语句的函数会被特地编译成生成器(generator)。当函数被调用时，他们返回一个生成器对象，这个对象支持迭代器接口
- `for in` `Python`专门将关键字for用作了迭代器的语法糖。在for循环中，Python将自动调用工厂函数iter()获得迭代器（`iter（）`调用对象内部的`__iter__`方法），自动调用`next()`获取元素，还完成了检查`StopIteration`异常（迭代终止）的工作。

![][2]


[1]: http://78rbbi.com1.z0.glb.clouddn.com/%E8%BF%AD%E4%BB%A3%E5%99%A8.png
[2]: http://78rbbi.com1.z0.glb.clouddn.com/6041cafeg8d35b1cee3d0&690.jpg
