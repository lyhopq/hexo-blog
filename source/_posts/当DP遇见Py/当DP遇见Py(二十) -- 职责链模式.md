title: 当DP遇见Py(二十) -- 职责链模式
toc: false
mathjax: false
date: 2015-09-04 14:24:51
categories: Python
tags: [Python, C++, DP]
---

## 定义：

> 使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这个对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。

## 类图：

![](http://78rbbi.com1.z0.glb.clouddn.com/chain.png)

## 类型：行为型

## 实例：

> 请假由谁批要看天数？

<!-- more -->

### C++ 实现

```C++
#include<iostream>
#include <string>
using namespace std;

class Request
{
public:
	string requestType;
	string requestContent;
	int number;
};

class Manager
{
public:
	Manager(string name)
	{
		this->name=name;
	}
	void setSuperior(Manager *superior)
	{
		this->superior=superior;
	}
	virtual void handleRequest(Request *request)=0;
protected:
    string name;
    Manager *superior;
};

class CommonManager:public Manager
{
public:
	CommonManager(string name):Manager(name)
	{}
	void handleRequest(Request *request)
	{
		if(request->requestType=="请假" && request->number<=2)
		{
			cout<<name<<" "<<request->requestContent<<" 数量: "<<request->number<<"被批准"<<endl;
		}
		else 
		{
			if(superior!=NULL)
			{
				superior->handleRequest(request);
			}
		}
	}
};

class Majordomo:public Manager
{
public:
	Majordomo(string name):Manager(name)
	{}
	void handleRequest(Request *request)
	{
		if(request->requestType=="请假" && request->number<=5)
		{
			cout<<name<<" "<<request->requestContent<<" 数量: "<<request->number<<"被批准"<<endl;
		}
		else 
		{
			if(superior!=NULL)
			{
				superior->handleRequest(request);
			}
		}
	}
};


class GeneralManager:public Manager
{
public:
	GeneralManager(string name):Manager(name)
	{}
	void handleRequest(Request *request)
	{
		if(request->requestType=="请假")
		{
			cout<<name<<" "<<request->requestContent<<" 数量: "<<request->number<<"被批准"<<endl;
		}
	}
};


int main()
{
	CommonManager *jinli=new CommonManager("经理"); 
	Majordomo *zongjian=new Majordomo("总监");
	GeneralManager *zhongjingli=new GeneralManager("总经理");
	
	jinli->setSuperior(zongjian);
	zongjian->setSuperior(zhongjingli);
	
	Request *request=new Request();

	request->requestType="请假";
	request->requestContent="李俊宏请假";
	request->number=1;
	jinli->handleRequest(request);


	request->requestType="请假";
	request->requestContent="李俊宏请假";
	request->number=4;
	jinli->handleRequest(request);


	request->requestType="请假";
	request->requestContent="李俊宏请假";
	request->number=10;
	jinli->handleRequest(request);


	return 0;
}
```

### Python 实现

```python
# -*- coding=utf-8 -*-

class Request(object):
    def __init__(self):
        self.requestType = ''
        self.requestContent = ''
        self.number = 0

class Manager(object):
    def __init__(self, name):
        self.name = name
        self.superior = None
    def setSuperior(self, superior):
        self.superior = superior
    def handleRequest(self, request):
        raise NotImplementedError()

class CommonManager(Manager):
    def __init__(self, name):
        super(CommonManager, self).__init__(name)
    def handleRequest(self, request):
        if request.requestType==u'请假' and request.number<=2:
            print u'{} {} 数量：{} 被批准'.format(self.name,
                                            request.requestContent,
                                            request.number)
        else:
            if self.superior:
                self.superior.handleRequest(request)

class Majordomo(Manager):
    def __init__(self, name):
        super(Majordomo, self).__init__(name)
    def handleRequest(self, request):
        if request.requestType==u'请假' and request.number<=5:
            print u'{} {} 数量：{} 被批准'.format(self.name,
                                            request.requestContent,
                                            request.number)
        else:
            if self.superior:
                self.superior.handleRequest(request)

class GeneralManager(Manager):
    def __init__(self, name):
        super(GeneralManager, self).__init__(name)
    def handleRequest(self, request):
        if request.requestType==u'请假':
            print u'{} {} 数量：{} 被批准'.format(self.name,
                                            request.requestContent,
                                            request.number)

if __name__ == '__main__':
    jinli = CommonManager(u"经理")
    zongjian = Majordomo(u"总监")
    zhongjingli = GeneralManager(u"总经理")

    jinli.setSuperior(zongjian)
    zongjian.setSuperior(zhongjingli)

    request = Request()

    request.requestType=u"请假"
    request.requestContent=u"李俊宏请假"
    request.number=1
    jinli.handleRequest(request)

    request.requestType=u"请假"
    request.requestContent=u"李俊宏请假"
    request.number=4
    jinli.handleRequest(request)

    request.requestType=u"请假"
    request.requestContent=u"李俊宏请假"
    request.number=10
    jinli.handleRequest(request)
```

### 执行结果：

```python
经理 李俊宏请假 数量：1 被批准
总监 李俊宏请假 数量：4 被批准
总经理 李俊宏请假 数量：10 被批准
```

## Tips:

`职责链模式`的`Python`实现没有什么特点，在这就不在赘述了。

![](http://78rbbi.com1.z0.glb.clouddn.com/chain1.png)
