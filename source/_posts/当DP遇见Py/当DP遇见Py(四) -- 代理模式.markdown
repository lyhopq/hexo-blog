title: 当DP遇见Py(四) -- 代理模式
date: 2014-12-15
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 为其它对象提供一种代理以控制对这个对象的访问。

## 类图：
![][1]

## 类型：结构型

<!-- more -->

## 实例：
> 给“小菜”一件一件穿衣服

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class SchoolGirl
{
public:
	string name;
};


class IGiveGift
{
public:
	virtual void giveDolls()=0;
	virtual void giveFlowers()=0;
};

class Pursuit:public IGiveGift
{
private:
	SchoolGirl mm;
public:
	Pursuit(SchoolGirl m)
	{
		mm=m;
	}
	void giveDolls()
	{
		cout<<mm.name<<" 送你洋娃娃"<<endl;	
	}
	void giveFlowers()
	{
		cout<<mm.name<<" 送你鲜花"<<endl;	
	}
};

class Proxy:public IGiveGift
{
private:
	Pursuit gg;
public:
	Proxy(SchoolGirl mm):gg(mm)
	{
	}
	void giveDolls()
	{
		gg.giveDolls();
	}
	void giveFlowers()
	{
		gg.giveFlowers();
	}
};

int main()
{
	SchoolGirl lijiaojiao;
	lijiaojiao.name="李娇娇";
	Pursuit zhuojiayi(lijiaojiao); 
	Proxy daili(lijiaojiao);

	daili.giveDolls();
	return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class SchoolGirl:
    def __init__(self, name):
        self.name = name

class IGiveGift():
    def giveDolls(self):
        raise NotImplementedError()

    def giveFlowers(self):
        raise NotImplementedError()

class Pursuit(IGiveGift):
    def __init__(self, m):
        self.mm = m

    def giveDolls(self):
        print '%s 送你洋娃娃' % self.mm.name

    def giveFlowers(self):
        print '%s 送你鲜花' % self.mm.name

class Proxy(IGiveGift):
    def __init__(self, mm):
        self.gg = Pursuit(mm)

    def giveDolls(self):
        self.gg.giveDolls()

    def giveFlowers(self):
        self.gg.giveFlowers()

if __name__ == '__main__':
    lijiaojiao = SchoolGirl('李娇娇')
    daili = Proxy(lijiaojiao)
    daili.giveDolls()
    daili.giveFlowers()
```

### Pythonic 实现
```python
# -*- coding=utf-8 -*-

class SchoolGirl:
    def __init__(self, name):
        self.name = name

class Pursuit:
    def __init__(self, m):
        self.mm = m

    def giveDolls(self):
        print '%s 送你洋娃娃' % self.mm.name

    def giveFlowers(self):
        print '%s 送你鲜花' % self.mm.name

class Proxy():
    def __init__(self, mm):
        self.gg = Pursuit(mm)

    def __getattr__(self, name):
        return getattr(self.gg, name)

if __name__ == '__main__':
    lijiaojiao = SchoolGirl('李娇娇')
    daili = Proxy(lijiaojiao)
    daili.giveDolls()
    daili.giveFlowers()
```

## Tips:

- Python中的委派机制(`delegation`）使得`Proxy`的实现可以非常的简洁优美。如上所示，利用`__getattr__`，使得程序具有完整的通用性(generic)。这是动态语言特有的优势，详见`Python的自省`。
- `__getattr__`方法。当尝试调用一个不存在的方法时，Python会默认调用`__getattr__`。如，当调用`daili.giveDolls()`时，对象`daili`中并不存在`giveDolls()`这个方法，`Python`解释器就是去调用`__getattr__`方法。在`__getattr__`方法中，我们通过`getattr`内置函数来调用被代理对象，即`self.gg`的同名方法，来实现代理。
- 魔术方法。 什么是魔术方法？它们是`Python`中的一些特殊方法，名字是以双下划线开始和结束，这些方法和类中的其他方法没有什么不同，只是在某些情况下类对象会自动调用这些特殊的方法。如，上所述的`__getattr__`方法和`__init__`方法（在对象初始化是调用）。魔术方法是`Python`面向对象的一切，想要弄清楚`Python`的面向对象是怎么来的，那就得好好研究`Python`的魔术方法。


`Python`的`Proxy`实现就是这么简单。好用就是这么任性！

[1]: http://78rbbi.com1.z0.glb.clouddn.com/代理模式.png
