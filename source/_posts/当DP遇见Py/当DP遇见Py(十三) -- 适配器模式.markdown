title: 当DP遇见Py(十三) -- 适配器模式
date: 2015-3-22
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 将一个类的接口转换为客户需要的另外一个接口。`Adapter`模式使得原本由于接口不兼容而不能在一起工作的那些类可以在一起工作。

## 类图：
![][1]

## 类型：结构型

<!-- more -->

## 实例：
> “在NBA，我需要翻译。” —— 姚明

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class Player
{
public:
	string name;
	Player(string name)
	{
		this->name=name;
	}
	virtual void attack()=0;
	virtual void defence()=0;
}; 

class Forwards:public Player
{
public:
	Forwards(string name):Player(name){}
	void attack()
	{
		cout<<name<<" 前锋进攻"<<endl;
	}
	void defence()
	{
		cout<<name<<" 前锋防守"<<endl;
	}
};

class Center:public Player
{
public:
	Center(string name):Player(name){}
	void attack()
	{
		cout<<name<<" 中锋进攻"<<endl;
	}
	void defence()
	{
		cout<<name<<" 中锋防守"<<endl;
	}
};

class Backwards:public Player
{
public:
	Backwards(string name):Player(name){}
	void attack()
	{
		cout<<name<<" 后卫进攻"<<endl;
	}
	void defence()
	{
		cout<<name<<" 后卫防守"<<endl;
	}
};

class ForeignCenter
{
public:
	string name;
	ForeignCenter(string name)
	{
		this->name=name;
	}
	void myAttack()
	{
		cout<<name<<" 外籍中锋进攻"<<endl;
	}
	void myDefence()
	{
		cout<<name<<" 外籍后卫防守"<<endl;
	}
};

class Translator:public Player
{
private:
	ForeignCenter *fc;
public:
	Translator(string name):Player(name)
	{
		fc=new ForeignCenter(name); 
	}
	void attack()
	{
		fc->myAttack();
	}
	void defence()
	{
		fc->myDefence();
	}
};

int main()
{
	Player *p1=new Center("巴蒂尔");
	p1->attack();
	p1->defence();
	
	Translator *tl=new Translator("姚明");
	tl->attack();
	tl->defence();
	
	return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class Player(object):
    def __init__(self, name):
        self._name = name
    def attack(self):
        raise NotImplementedError("Must Implement me")
    def defence(self):
        raise NotImplementedError("Must Implement me")

class Forwards(Player):
    def __init__(self, name):
        super(Forwards, self).__init__(name)
    def attack(self):
        print u'%s 前锋进攻' % self._name
    def defence(self):
        print u'%s 前锋防守' % self._name

class Center(Player):
    def __init__(self, name):
        super(Center, self).__init__(name)
    def attack(self):
            print u'%s 中锋进攻' % self._name
    def defence(self):
        print u'%s 中锋防守' % self._name

class ForeignCenter:
    def __init__(self, name):
        self._name = name
    def myAttack(self):
        print u'%s 外籍中锋进攻' % self._name
    def myDefence(self):
        print u'%s 外籍中锋防守' % self._name

class Translator(Player):
    def __init__(self, name):
        self._fc = ForeignCenter(name)
    def attack(self):
        self._fc.myAttack()
    def defence(self):
        self._fc.myDefence()

if __name__ == '__main__':
    p1 = Center(u'巴蒂尔')
    p1.attack()
    p1.defence()

    t1 = Translator(u'姚明')
    t1.attack()
    t1.defence()
```

### Pythonic 实现
```python
# -*- coding=utf-8 -*-

class Center:
    def __init__(self, name):
        self._name = name
    def attack(self):
        print u'%s 中锋进攻' % self._name
    def defence(self):
        print u'%s 中锋防守' % self._name

class ForeignCenter:
    def __init__(self, name):
        self._name = name
    def myAttack(self):
        print u'%s 外籍中锋进攻' % self._name
    def myDefence(self):
        print u'%s 外籍中锋防守' % self._name

class Adapter(object):
    def __init__(self, adaptee, **adapted_methods):
        self.adaptee  = adaptee
        self.__dict__.update(adapted_methods)

    def __getattr__(self, attr):
        return getattr(self.adaptee, attr)

if __name__ == '__main__':
    players = []
    players.append(Center(u'巴蒂尔'))
    fc = ForeignCenter(u'姚明')
    players.append(Adapter(fc, attack=fc.myAttack, defence=fc.myDefence))

    for player in players:
        player.attack()
        player.defence()
```


### 执行结果：
```python
巴蒂尔 中锋进攻
巴蒂尔 中锋防守
姚明 外籍中锋进攻
姚明 外籍中锋防守
```

## Tips:

`适配器模式`的`Python`实现没有什么特点，在这就不在赘述了。
`Pythonic`的`适配器模式`的实现，充分运用了`Python`的特性，提供了一个高度抽象的适配器：`Adapter`。让我们看一下`Adapter`是如何实现： “将一个类的接口转换为客户需要的另外一个接口”的。

- `Adaptee` 被适配的对象
- `adapted_methods` 被适配的方法。由客户指定`Adaptee`的那些方法适配为客户需要的接口。如需要`ForeignCenter`对象的`myAttack`方法适配为客户所需要的`attack`接口。
- `**kwargs`是一个正常的python字典类型，允许用户通过键值对来传递参数。如：`attack=fc.myAttack, defence=fc.myDefence`
- `__dict__` 和 `__getattr__`是实现适配的关键。这里的主要作用是更新用户设置的适配方法(`adapted_methods`)到适配器(`Adapter`)的方法字典中，用户未设置的方法仍然使用被适配对象(`adaptee`)的方法。这两个东西是`Python`中面向对象的关键，在前面的帖子中已经介绍过了，在此不在赘述。

![][2]

请找出上图中那个东东是“适配器”。


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/适配器模式.png
  [2]: http://image.suning.cn/uimg/sop/commodity/201406191019167208_x.jpg
