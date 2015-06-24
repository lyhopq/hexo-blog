title: 当DP遇见Py(十) -- 观察者模式
date: 2015-3-01
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 定义了一种一对多的依赖关系，让多个观察对象同时监听一个主题对象。这个主题对象在状态发生变化时，会通知所有观察者对象,，使它们能够自动更新自己。

## 类图：
![此处输入图片的描述][1]

## 类型：行为型

<!-- more -->

## 实例：偷懒员工“观察”老板

### C++ 实现
```C++
#include <iostream>
#include <string>
#include <list>
using namespace std;

class Subject;

class Observer
{
protected:
    string name;
    Subject *sub;
public:
    Observer(string name, Subject *sub)
    {
        this->name=name;
        this->sub=sub;
    }
    virtual void update()=0;
};

class StockObserver:public Observer
{
public:
    StockObserver(string name, Subject *sub):Observer(name,sub)
    {
    }
    void update();
};

class NBAObserver:public Observer
{
public:
    NBAObserver(string name,Subject *sub):Observer(name,sub)
    {
    }
    void update();
};

class Subject
{
protected:
    list<Observer*> observers;
public:
    string action;
    virtual void attach(Observer*)=0;
    virtual void detach(Observer*)=0;
    virtual void notify()=0;
};

class Secretary:public Subject
{
    void attach(Observer *observer)
    {
        observers.push_back(observer);
    }
    void detach(Observer *observer)
    {
        list<Observer *>::iterator iter=observers.begin();
        while(iter!=observers.end())
        {
            if((*iter)==observer)
            {
                observers.erase(iter);
            }
            ++iter;
        }
    }
    void notify()
    {
        list<Observer *>::iterator iter=observers.begin();
        while(iter!=observers.end())
        {
            (*iter)->update();
            ++iter;
        }
    }
};

void StockObserver::update()
{
    cout<<name<<" 收到消息："<<sub->action<<endl;
    if(sub->action=="梁所长来了!")
    {
        cout<<"我马上关闭股票，装做很认真工作的样子！"<<endl;
    }
}

void NBAObserver::update()
{
    cout<<name<<" 收到消息："<<sub->action<<endl;
    if(sub->action=="梁所长来了!")
    {
        cout<<"我马上关闭NBA，装做很认真工作的样子！"<<endl;
    }
}

int main()
{
    Subject *dwq=new Secretary();
    Observer *xs=new NBAObserver("xiaoshuai",dwq);
    Observer *zy=new NBAObserver("zouyue",dwq);
    Observer *lm=new StockObserver("limin",dwq);

    dwq->attach(xs);
    dwq->attach(zy);
    dwq->attach(lm);

    dwq->action="去吃饭了！";
    dwq->notify();
    cout<<endl;
    dwq->action="梁所长来了!";
    dwq->notify();
    return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class Subject(object):
    def __init__(self):
        self._observers = []

    def attach(self, observer):
        if observer not in self._observers:
            self._observers.append(observer)

    def detach(self, observer):
        try:
            self._observers.remove(observer)
        except ValueError:
            pass

    def notify(self, modifier=None):
        for observer in self._observers:
            if modifier != observer:
                observer.update()

class Secretary(Subject):
    def __init__(self, name=''):
        super(Secretary, self).__init__()

class Observer(object):
    def __init__(self, name, sub):
        self._name = name
        self._sub = sub

    def update(self):
        raise NotImplementedError("Must Implement me")

class StockObserver(Observer):
    def __init__(self, name, sub):
        super(StockObserver, self).__init__(name, sub)

    def update(self):
        print '%s 收到消息：%s' % (self._name, self._sub.action)
        if self._sub.action == "梁所长来了!":
            print '我马上关闭股票，装做很认真工作的样子！'

class NBAObserver(Observer):
    def __init__(self, name, sub):
        super(NBAObserver, self).__init__(name, sub)

    def update(self):
        print '%s 收到消息：%s' % (self._name, self._sub.action)
        if self._sub.action == "梁所长来了!":
            print '我马上关闭NBA，装做很认真工作的样子！'


if __name__ == '__main__':
    dwq = Secretary()
    xs = NBAObserver("xiaoshuai", dwq)
    zy = NBAObserver("zouyue", dwq)
    lm = StockObserver("limin", dwq)

    dwq.attach(xs)
    dwq.attach(zy)
    dwq.attach(lm)

    dwq.action = "去吃饭了！"
    dwq.notify()

    dwq.action = "梁所长来了!"
    dwq.notify()
```

### 执行结果：
```python
xiaoshuai 收到消息：去吃饭了！
zouyue 收到消息：去吃饭了！
limin 收到消息：去吃饭了！
xiaoshuai 收到消息：梁所长来了!
我马上关闭NBA，装做很认真工作的样子！
zouyue 收到消息：梁所长来了!
我马上关闭NBA，装做很认真工作的样子！
limin 收到消息：梁所长来了!
我马上关闭股票，装做很认真工作的样子！
```

## Tips:
`观察者模式`的`Python`实现没有什么特点，在这就不在赘述了。

![此处输入图片的描述][2]


  [1]: http://images.cnblogs.com/cnblogs_com/wuyuegb2312/468244/o_ch10.%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F.png
  [2]: http://doc.okbase.net/picture/addon/2013/08/04/A004202772-20994.png