title: 当DP遇见Py(十八) -- 桥接模式
toc: false
mathjax: false
date: 2015-08-08 17:21:58
categories: Python
tags: [Python,DP,C++]

---

## 定义：

> 将抽象部分和它的实现部分分离，使它们可以独立的变化。

## 类图：

![](http://78rbbi.com1.z0.glb.clouddn.com/bridge.png)

## 类型：结构型

## 实例：

> 两种品牌的手机，运行相同的软件

<!-- more -->

### C++ 实现

```C++
#include <iostream>
#include <string>
using namespace std;

class HandsetSoft
{
public:
    virtual void run()=0;
};

class HandsetGame:public HandsetSoft
{
public:
    void run()
    {
        cout<<"运行手机游戏"<<endl;
    }
};

class HandsetAddressList:public HandsetSoft
{
public:
    void run()
    {
        cout<<"运行手机通讯录"<<endl;
    }
};

class HandsetBrand
{
protected:
    HandsetSoft *soft;
public:
    void setHandsetSoft(HandsetSoft *soft)
    {
        this->soft=soft;
    }
    virtual void run()=0;
};

class HandsetBrandN:public HandsetBrand
{
public:
    void run()
    {
        cout<<"手机品牌N：";
        soft->run();
    }
};

class HandsetBrandM:public HandsetBrand
{
public:
    void run()
    {
        cout<<"手机品牌M：";
        soft->run();
    }
};

int main()
{
    HandsetBrand *hb;
    
    hb=new HandsetBrandN();
    hb->setHandsetSoft(new HandsetGame());
    hb->run();
    hb->setHandsetSoft(new HandsetAddressList());
    hb->run();

    hb=new HandsetBrandM();
    hb->setHandsetSoft(new HandsetGame());
    hb->run();
    hb->setHandsetSoft(new HandsetAddressList());
    hb->run();

    return 0;
}
```

### Python 实现

```python
# -*- coding=utf-8 -*-

class HandsetSoft(object):
    def Run(self):
        pass

class HandsetGame(HandsetSoft):
    def Run(self):
        print u"运行手机游戏"

class HandsetAddressList(HandsetSoft):
    def Run(self):
        print u"运行手机通讯录"

class HandsetBrand(object):
    def __init__(self):
        self.m_soft = None
    def SetHandsetSoft(self,temp):
        self.m_soft= temp
    def Run(self):
        pass

class HandsetBrandN(HandsetBrand):
    def Run(self):
        if not (self.m_soft == None):
            print u"手机品牌N：",
            self.m_soft.Run()

class HandsetBrandM(HandsetBrand):
    def Run(self):
        if not (self.m_soft == None):
            print u"手机品牌M：",
            self.m_soft.Run()

if __name__ == "__main__":
    brand = HandsetBrandN()
    brand.SetHandsetSoft(HandsetGame())
    brand.Run()
    brand.SetHandsetSoft(HandsetAddressList())
    brand.Run()    

    brand = HandsetBrandM()
    brand.SetHandsetSoft(HandsetGame())
    brand.Run()
    brand.SetHandsetSoft(HandsetAddressList())
    brand.Run()    
```

### 执行结果：

```python
手机品牌N： 运行手机游戏
手机品牌N： 运行手机通讯录
手机品牌M： 运行手机游戏
手机品牌M： 运行手机通讯录
```

## Tips:

`桥接模式`的`Python`实现没有什么特点，在这就不在赘述了。

![](http://78rbbi.com1.z0.glb.clouddn.com/12VF55EP5Z-332S4.jpg)
