title: 当DP遇见Py(三) -- 装饰模式
date: 2014-12-08
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更为灵活。

## 类图：
![此处输入图片的描述][1]

## 类型：结构型

<!-- more -->

## 实例：
> 给“小菜”一件一件穿衣服

### C++ 实现
```C++
#include <string>
#include <iostream>
using namespace std;

class Person
{
private:
	string name;
public:
	Person(string name)
	{
		this->name = name;
	}
	Person(){}
	virtual void show()
	{
		cout<<"装扮的是:" << name << endl;
	}
};	

class Finery:public Person
{
protected:
	Person *component;
public:
	void decorate(Person* component)
	{
		this->component = component;
	}
	virtual void show()
	{
		component->show();
	}
};

class TShirts:public Finery
{
public:
	virtual void show()
	{
		component->show();
		cout << "T shirts" << endl;
	}
};

class BigTrouser:public Finery
{
public:
	virtual void show()
	{
		component->show();
		cout << "Big Trouser" << endl;
	}
};

int main()
{
	Person *p = new Person("小菜");
	BigTrouser *bt = new BigTrouser();
	TShirts *ts = new TShirts();
		
	bt->decorate(p);
	ts->decorate(bt);
	ts->show();
	
	return 0;
}
```

### Python 实现
```Python
# -*- coding=utf-8 -*-

class Person(object):
    def __init__(self, name):
        self.name = name

    def show(self):
        print '装扮的是：%s'  % self.name

class Finery(Person):
    def __init__(self):
        self.component = None

    def decorate(self, component):
        self.component  = component

    def show(self):
        self.component.show()

class TShirts(Finery):
    def show(self):
        self.component.show()
        print 'T shirts'

class BigTrouser(Finery):
    def show(self):
        self.component.show()
        print 'Big Trouser'

if __name__ == '__main__':
    p = Person('小菜')
    bt = BigTrouser()
    ts = TShirts()

    bt.decorate(p)
    ts.decorate(bt)
    ts.show()
```

### Pythonic 实现
```Python
# -*- coding=utf-8 -*-
from functools import wraps

def dress(clothes):
    def wrapped(fn):
        @wraps(fn)
        def _wrapped(*args):
            fn(*args)
            print clothes
        return _wrapped
    return wrapped

@dress('T shirts')
@dress('Big Trouser')
def finery(name):
    print '装扮的是： %s'  % name

if __name__ == '__main__':
    finery('小菜')
```

### 运行结果
```
装扮的是：小菜
Big Trouser
T shirts
```

## Tips:
### 装饰器
Python的修饰器的英文名叫Decorator，当你看到这个英文名的时候，你可能会把其跟Design Pattern里的Decorator搞混了，其实这是完全不同的两个东西。但是，他们要干的事都很相似——都是想要对一个已有的模块做一些“修饰工作”。Python 的 Decorator使用很简单，就是在方法名前面加一个`@XXX`注解来为这个方法装饰一些东西。对于Python的这个`@`注解语法糖- Syntactic Sugar 来说，当你在用某个@decorator来修饰某个函数func时，如下所示:
```python
@decorator
def func():
    pass
```
Python解释器会解释成下面这样的语句：
```python
func = decorator(func)
```
看看上面的语法，这其实就是把一个函数当参数传到另一个函数中，然后再回调！是的，但是，我们需要注意，这里还有一个赋值语句，把decorator这个函数的返回值赋值回了原来的func。
这个语法是不是似曾相识，是的，在`当DP遇见Py（二）`中就有他的身影——`高阶函数`和`闭包`。
就我们上面那个例子来说,
```python
@dress('T shirts')
@dress('Big Trouser')
def finery(name):
    print '装扮的是： %s'  % name
```
被解释成了：
```
finery = dress('T shirts')(dress('Big Trouser')(finery))
```
Python的Decorator就是这么简单，没有什么复杂的东西，你也不需要了解过多的东西，使用起来就是那么自然、简洁。

  [1]: http://images.cnblogs.com/cnblogs_com/wuyuegb2312/468244/o_ch3.%E8%A3%85%E9%A5%B0%E6%A8%A1%E5%BC%8F.png