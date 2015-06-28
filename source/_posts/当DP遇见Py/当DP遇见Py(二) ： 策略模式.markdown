title: 当DP遇见Py(二) -- 策略模式
date: 2014-11-28
categories: Python
tags: [Python, C++, DP]

---

## 定义

> 策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

## 类图
![][1]

## 类型：行为型

<!-- more -->

## 实例
> 商场收银软件，需要根据不同的销售策略方式进行收费

### C++ 实现

```C++
#include <iostream>
#include <cmath>
#include <string>
using namespace std;

class CashSuper
{
public:
	virtual double acceptMoney(double money)=0;
};

class CashNormal:public CashSuper
{
public:
	double acceptMoney(double money)
	{
		return money;
	}
};

class CashRebate:public CashSuper
{
private:
	double discount;
public:
	CashRebate(double dis)
	{
		discount = dis;
	}
	double acceptMoney(double money)
	{
		return money * discount;
	}
};


class CashReturn:public CashSuper
{
private:
	double moneyCondition;
	double moneyReturn;
public:
	CashReturn(double mc,double mr)
	{
		moneyCondition = mc;
		moneyReturn = mr;
	}
	double acceptMoney(double money)
	{
		double result = money;
		if(money >= moneyCondition)
		{
			result = money - floor(money / moneyCondition) * moneyReturn;
		}
		return result;
	}
};

class  CashContext
{
private:
	CashSuper *cs;
public:
	CashContext(CashSuper *cs)
	{
		this->cs = cs;
	}
	double getResult(double money)
	{
		return cs->acceptMoney(money);
	}
};

int main()
{
	CashSuper *cs;
	CashContext *cc;
	double money;
	
	money = 1000;
	cs = new CashRebate(0.8);
	cc = new CashContext(cs);
	cout << cc->getResult(money) << endl;

	money = 1000;
	cs = new CashNormal();
	cc = new CashContext(cs);
	cout << cc->getResult(money) << endl;


	return 0;
}
```

### Python 实现

```python
from math import floor

class CashSuper(object):
    def acceptMoney(self, money):
        return 0

class CashNormal(CashSuper):
    def acceptMoney(self, money):
        return money

class CashRebate(CashSuper):
    def __init__(self, discount):
        self._discount = discount

    def acceptMoney(self, money):
        return money * self._discount

class CashReturn(CashSuper):
    def __init__(self, mc, mr):
        self._moneyCondition = mc
        self._moneyReturn = mr

    def acceptMoney(self, money):
        if money >= self._moneyCondition:
            return money - floor(money / self._moneyCondition) * self._moneyReturn
        else:
            return money

class CashContext:
    def __init__(self, cs):
        self._cs = cs

    def getResult(self, money):
        return self._cs.acceptMoney(money)

if __name__ == '__main__':
    money = 1000

    cc = CashContext(CashRebate(0.8))
    print cc.getResult(money)

    cc = CashContext(CashNormal())
    print cc.getResult(money)
```

### Pythonic 实现

```python
class CashContext:
    def __init__(self, cs):
        self._cs = cs

    def getResult(self, money):
        return self._cs(money)

def cashNormal():
    def accpetMoney(money):
        return money
    return accpetMoney

def cashRebate(discount):
    def accpetMoney(money):
        return money * discount
    return accpetMoney

def cashReturn(mc, mr):
    def accpetMoney(money):
        if money >= mc:
            return money - floor(money / mc) * mr
        else:
            return money
    return accpetMoney


if __name__ == '__main__':
    money = 1000
    
    cc = CashContext(cashNormal())
    print cc.getResult(money)

    cc = CashContext(cashRebate(0.8))
    print cc.getResult(money)

    cc = CashContext(cashReturn(300, 100))
    print cc.getResult(money)
```

## Tips:
- 在大多数的编程语言中，策略模式实现是这样的： 首先创建一个基础策略（通过接口或抽象类），然后创建若干子类继承这个基础策略。然而，`Python`支持高阶函数，可以使我们仅仅使用一个类，并把不同的函数注入该类的对象中来实现策略模式。详见：`Pythonic` 实现
- 高阶函数（`higher-order functions`）。所谓高阶函数，就是操作函数的函数，一个函数可以接受另一个函数作为参数，也可以把一个函数作为结果来返回。
- 闭包(`Closures`)。在`cashNormal`、`cashReturn`等函数中定义了一个函数`accpetMoney`，我们说`accpetMoney`是`cashNormal`、`cashReturn`等的内部函数。在`accpetMoney`的局部作用域中可以直接访问`cashReturn`局部作用域中定义的`mc`、`mr`变量。简单的说，这种内部函数可以使用外部函数变量的行为，就叫闭包。
- 函数式编程（Functional programming）。高阶函数和闭包都是函数式编程的特点。因此Python支持函数式编程。关于Python如何支持函数式编程值得深入研究，有兴趣的同学可以查阅相关资料。

[1]: http://78rbbi.com1.z0.glb.clouddn.com/策略模式.png
