title: 当DP遇见Py(十二) -- 状态模式
date: 2015-3-13
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。 

## 类图：
![][1]

## 类型：行为型

<!-- more -->

## 实例：
> 程序员 的一天

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class Work;
class State;
class ForenonnState;

class State
{
public:
    virtual void writeProgram(Work*)=0;
};

class Work
{
public:
    int hour;
    State *current;
    Work();
    void writeProgram()
    {
        current->writeProgram(this);
    }
};

class EveningState:public State
{
public:
    void writeProgram(Work *w)
    {
        cout<<"当前时间: "<<w->hour<<"点 "<<"心情很好，在看《天天向上》，收获很大！"<<endl;
    }
};

class AfternoonState:public State
{
public:
    void writeProgram(Work *w)
    {
        if(w->hour<19)
        {
            cout<<"当前时间: "<<w->hour<<"点 "<<"下午午睡后，工作还是精神百倍!"<<endl;
        }
        else
        {
            w->current=new EveningState();
            w->writeProgram();
        }
    }
};

class ForenonnState:public State
{
public:
    void writeProgram(Work *w)
    {
        if(w->hour<12)
        {
            cout<<"当前时间: "<<w->hour<<"点 "<<"上午工作精神百倍!"<<endl;
        }
        else
        {
            w->current=new AfternoonState();
            w->writeProgram();
        }
    }
};

Work::Work()
{
    current=new ForenonnState();
}

int main()
{
    Work *w=new Work();
    w->hour=9;
    w->writeProgram();
    w->hour=14;
    w->writeProgram();
    w->hour=21;
    w->writeProgram();
    return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class State:
    def writeProgram(self, w):
        raise NotImplementedError("Must Implement me")

class ForenonnState(State):
    def writeProgram(self, w):
        if w.hour < 12:
            print u"当前时间: %s点 上午工作精神百倍!" % w.hour
        else:
            w.current = AfternoonState()
            w.writeProgram()

class AfternoonState(State):
    def writeProgram(self, w):
        if w.hour < 19:
            print u"当前时间: %s点 下午午睡后，工作还是精神百倍!" % w.hour
        else:
            w.current = EveningState()
            w.writeProgram()

class EveningState(State):
    def writeProgram(self, w):
        print u"当前时间: %s点 心情很好，在看《天天向上》，收获很大！" % w.hour

class Work:
    def __init__(self):
        self.hour = 9
        self.current = ForenonnState()
    def setState(self, state):
        self.current = state
    def writeProgram(self):
        self.current.writeProgram(self)

if __name__ == "__main__":
    mywork = Work()
    mywork.hour = 9
    mywork.writeProgram()
    mywork.hour = 14
    mywork.writeProgram()
    mywork.hour = 21
    mywork.writeProgram()
```

### 执行结果：
```python
当前时间: 9点 上午工作精神百倍!
当前时间: 14点 下午午睡后，工作还是精神百倍!
当前时间: 21点 心情很好，在看《天天向上》，收获很大！
```

## Tips:

`状态模式`的`Python`实现没有什么特点，在这就不在赘述了。

![][2]


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/状态模式.png
  [2]: http://i1.brcdn.cn/download/2012/02/02/baoruan.com_53889ae89abab9bda.gif
