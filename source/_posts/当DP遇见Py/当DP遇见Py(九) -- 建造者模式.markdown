title: 当DP遇见Py(九) -- 建造者模式
date: 2015-2-08
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

## 类图：
![此处输入图片的描述][1]

## 类型：创建型

<!-- more -->

## 实例：“画”出一个四肢健全（头身手腿）的小人

### C++ 实现
```C++
#include <string>
#include <iostream>
#include <vector>
using namespace std;


class Person
{
public:
	virtual void createHead()=0;
	virtual void createHand()=0;
	virtual void createBody()=0;
	virtual void createFoot()=0;
};

class ThinPerson:public Person
{
	void createHead()
	{
		cout<<"thin head"<<endl;
	}
	void createHand()
	{
		cout<<"thin hand"<<endl;
	}
	void createBody()
	{
		cout<<"thin body"<<endl;
	}
	void createFoot()
	{
		cout<<"thin foot"<<endl;
	}
};

class FatPerson:public Person
{
	void createHead()
	{
		cout<<"fat head"<<endl;
	}
	void createHand()
	{
		cout<<"fat hand"<<endl;
	}
	void createBody()
	{
		cout<<"fat body"<<endl;
	}
	void createFoot()
	{
		cout<<"fat foot"<<endl;
	}
};

class Director
{
private:
	Person *p;
public:
	Director(Person *temp)
	{
		p=temp;
	}
	void create()
	{
		p->createHead();
		p->createHand();
		p->createBody();
		p->createFoot();
	}
};

int main()
{
    Person *p=new FatPerson();
	Director *d= new Director(p);
	d->create();
	delete d;
	delete p;
	return 0;
}
```

### Python 实现
```python
class Person:
    def CreateHead(self):
        pass
    def CreateHand(self):
        pass
    def CreateBody(self):
        pass
    def CreateFoot(self):
        pass

class ThinPerson(Person):
    def CreateHead(self):
        print "thin head"
    def CreateHand(self):
        print "thin hand"
    def CreateBody(self):
        print "thin body"
    def CreateFoot(self):
        print "thin foot"

class ThickPerson(Person):
    def CreateHead(self):
        print "thick head"
    def CreateHand(self):
        print "thick hand"
    def CreateBody(self):
        print "thick body"
    def CreateFoot(self):
        print "thick foot"

class Director:
    def __init__(self,temp):
        self.p = temp
    def Create(self):
        self.p.CreateHead()
        self.p.CreateBody()
        self.p.CreateHand()
        self.p.CreateFoot()

if __name__ == "__main__":
    p = ThickPerson()
    d = Director(p)
    d.Create()
```

### 执行结果：
```python
thick head
thick body
thick hand
thick foot
```

## Tips:
`创建者模式`的`Python`实现没有什么特点，在这就不在赘述了。

![此处输入图片的描述][1]


  [1]: http://www.tgmj.net/images/cp441/1.gif