title: 当DP遇见Py(一) -- 简单工厂模式
date: 2014-11-21
categories: Python
tags: [Python, C++, DP]

---

## 前言

什么是设计模式？
> 
设计模式（Design Pattern）的提出，是面向对象程序设计演化过程中的一个重要里程碑。正如Gamma，Helm，Johnson和Vlissides在他们的经典著作《设计模式》一书中所说的：设计模式使得人们可以更加简单和方便地去复用成功的软件设计和体系结构，从而能够帮助设计者更快更好地完成系统设计。

Python 与设计模式

设计模式是一种设计思想，语言是实现思想的工具。因此，不同语言的特性影响了设计模式的实现，有些语言更容易实现设计模式，而有些语言则比较难。
在讲设计模式的书籍中基本上都是以`静态语言`来实现的，如：《设计模式》主要以`C++`为主、《Head First 设计模式》使用Java、《大话设计模式》使用`C#`等等。那么，以`Python`为代表的`动态语言`怎么样来实现设计模式呢？
Python 是一种完全面向对象的动态语言，提供了与传统面向对象语言截然不同的对象模型，影响了设计模式的实现和使用。本系列尝试用Python语言来实现设计模式，并与C++的实现方式进行比较，进一步体现Python中的“动态性”及其具体应用。

<!-- more -->

具体安排：

- 以《大话设计模式》为蓝本，以Python(尽量做到Pythonic)实现书中呈现的25中设计模式
- 每一种设计模式的内容包括：
    - 模式定义、类图、类型
    - C++ 实现
    - Python实现及其关键点说明


Ok，就从简单工厂开始吧！
    



## 简单工厂模式

定义：
> 专门定义一个类来负责创建其它类的实例，被创建的实例通常都具有共同的父类。

类图：
![Simple Factory][1]

类型：创建者模式


### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class Operation
{
public:
	double op1, op2;
	virtual double getResult()
	{
		return 0;
	}
};

class addOperation:public Operation
{
	double getResult()
	{
		return op1 + op2;
	}
};

 
class subOperation:public Operation
{
	double getResult()
	{
		return op1 - op2;
	}
};

class mulOperation:public Operation
{
	double getResult()
	{
		return op1 * op2;
	}
};

class divOperation:public Operation
{
	double getResult()
	{
		return op1 / op2;
	}
};

class operFactory
{
public:
	static Operation *createOperation(char op)
	{
		switch(op)
		{
		case '+':
			return new addOperation;
			break;

		case '-':
			return new subOperation;
			break;

		case '*':
			return new mulOperation;
			break;

		case '/':
			return new divOperation;
			break;
		}
	}
};

int main()
{
	Operation *oper = operFactory::createOperation('+');
	oper->op1 = 9;
	oper->op2 = 99;
	cout << oper->getResult() << endl;
	return 0;
}
```

### Python 实现
```python
class Operation:
    def getResult(self):
        pass

class addOperation(Operation):
    def getResult(self):
        return self.op1 + self.op2

class subOperation(Operation):
    def getResult(self):
        return self.op1 - self.op2

class mulOperation(Operation):
    def getResult(self):
        return self.op1 * self.op2

class divOperation(Operation):
    def getResult(self):
        return self.op1 / self.op2

class operFactory:
    operation = {
        '+': addOperation,
        '-': subOperation,
        '*': mulOperation,
        '/': divOperation
    }

    @classmethod
    def createOperation(cls, op):
        if op in cls.operation:
            return cls.operation[op]()

if __name__ == '__main__':
    oper = operFactory.createOperation('+')
    oper.op1 = 9
    oper.op2 = 99
    print oper.getResult()
```


### Tips:

- Python一切皆对象。类也是对象（类是什么的对象呢？有机会可以深入说一说），因此你可以像使用对象一样的使用类，如在`operation`字典中直接使用`addOperation`、`suboperation`等类对象作为字典的值。
- `@classmethod` 是Python中的装饰器（在后续的模式中会详细介绍）语法。`@classmethod`表示该方法是类方法（与C++中的类的静态方法概念一致），可以直接使用类来调用，如：`operFactory.createOperation('+')`。
- `cls` 是类方法的第一个参数，表示类对象（注意，类也是对象）本身。类对象可以使用其它的名字代替，但Python社区惯用`cls`来表示。在使用类方法的时候第一个参数（`cls`），即类对象，不需要传递，该参数由Python解释器自动传递，如：`operFactory.createOperation('+')`。


[1]: http://78rbbi.com1.z0.glb.clouddn.com/简单工厂模式.png
