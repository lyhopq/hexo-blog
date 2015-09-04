title: 当DP遇见Py(十九) -- 命令模式
toc: false
mathjax: false
date: 2015-09-04 10:33:23
categories: Python
tags: [Python, C++, DP]
---


## 定义：

> 将一个请求封装为一个对象，从而使可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤消的操作。

## 类图：

![](http://78rbbi.com1.z0.glb.clouddn.com/command.png)

## 类型：行为型

## 实例：

> 烧烤店客户向服务员点单，服务员将点好的单告诉大厨，由大厨进行烹饪。

<!-- more -->

### C++ 实现

```C++
#include <iostream>
#include <string>
#include <list>

using namespace std;

class Barbecuer
{
public:
	void bakeMutton()
	{
		cout<<"烤羊肉串"<<endl;
	}
	void bakeChickenWing()
	{
		cout<<"烤鸡翅"<<endl;
	}
};

class Command
{
protected:
	Barbecuer *receiver;
public:
	Command(Barbecuer *receiver)
	{
		this->receiver=receiver;
	}
	virtual void executeCommand()=0;
};

class BakeMuttonCommand:public Command
{
public:
	BakeMuttonCommand(Barbecuer *receiver):Command(receiver)
	{}
	void executeCommand()
	{
		receiver->bakeMutton();
	}
};

class BakeChikenWingCommand:public Command
{
public:
	BakeChikenWingCommand(Barbecuer *receiver):Command(receiver)
	{}
	void executeCommand()
	{
		receiver->bakeChickenWing();
	}
};

class Waiter
{
private:
	list<Command*> orders;
public:
	void setOrder(Command *command)
	{
		orders.push_back(command);
	}
	
	void notify()
	{
		list<Command*>::iterator iter=orders.begin();
		while(iter!=orders.end())
		{
			(*iter)->executeCommand();
			iter++;
		}
	}
};

int main()
{
	
	Barbecuer *boy=new Barbecuer();
	Command *bm1=new BakeMuttonCommand(boy);
	Command *bm2=new BakeMuttonCommand(boy);
	Command *bc1=new BakeChikenWingCommand(boy);
	
	Waiter *girl=new Waiter();

	girl->setOrder(bm1);
 	girl->setOrder(bm2);
 	girl->setOrder(bc1);
	
	girl->notify();

	return 0;
}
```

### Python 实现

```python
# -*- coding=utf-8 -*-

class Barbecuer(object):
    def bakeMutton(self):
        print u'烤羊肉串'
    def bakeChickenWing(self):
        print u'烤鸡翅'

class Command(object):
    def __init__(self, receiver):
        self.receiver = receiver
    def execute(self):
        raise NotImplementedError()

class BakeMuttonCommand(Command):
    def __init__(self, receiver):
        super(BakeMuttonCommand, self).__init__(receiver)
    def execute(self):
        self.receiver.bakeMutton()

class BakeChikenWingCommand(Command):
    def __init__(self, receiver):
        super(BakeChikenWingCommand, self).__init__(receiver)
    def execute(self):
        self.receiver.bakeChickenWing()

class Waiter(object):
    def __init__(self):
        self.commands = []
    def setOrder(self, command):
        self.commands.append(command)
    def notify(self):
        for command in self.commands:
            command.execute()

if __name__ == '__main__':
    boy = Barbecuer()
    bm1 = BakeMuttonCommand(boy)
    bm2 = BakeMuttonCommand(boy)
    bc1 = BakeChikenWingCommand(boy)

    girl = Waiter()

    girl.setOrder(bm1)
    girl.setOrder(bm2)
    girl.setOrder(bc1)

    girl.notify()
```

### 执行结果：

```python
烤羊肉串
烤羊肉串
烤鸡翅
```

## Tips:

`命令模式`的`Python`实现没有什么特点，在这就不在赘述了。

![](http://78rbbi.com1.z0.glb.clouddn.com/c1.gif)
