title: 当DP遇见Py(十一) -- 抽象工厂模式
date: 2015-3-07
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们的类。
## 类图：

![][1]

## 类型：创建型

<!-- more -->

## 实例：
> 提供对不同的数据库访问的支持

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class IUser
{
public:
    virtual void getUser()=0;
    virtual void setUser()=0;
};

class SqlUser:public IUser
{
public:
    void getUser()
    {
        cout<<"在sql中返回user"<<endl;
    }
    void setUser()
    {
        cout<<"在sql中设置user"<<endl;
    }
};

class AccessUser:public IUser
{
public:
    void getUser()
    {
        cout<<"在Access中返回user"<<endl;
    }
    void setUser()
    {
        cout<<"在Access中设置user"<<endl;
    }
};

class IDepartment
{
public:
    virtual void getDepartment()=0;
    virtual void setDepartment()=0;
};

class SqlDepartment:public IDepartment
{
public:
    void getDepartment()
    {
        cout<<"在sql中返回Department"<<endl;
    }
    void setDepartment()
    {
        cout<<"在sql中设置Department"<<endl;
    }
};

class AccessDepartment:public IDepartment
{
public:
    void getDepartment()
    {
        cout<<"在Access中返回Department"<<endl;
    }
    void setDepartment()
    {
        cout<<"在Access中设置Department"<<endl;
    }
};

class IFactory
{
public:
    virtual IUser *createUser()=0;
    virtual IDepartment *createDepartment()=0;
};

class SqlFactory:public IFactory
{
public:
    IUser *createUser() 
    {
        return new SqlUser();
    }
    IDepartment *createDepartment() 
    {
        return new SqlDepartment();
    }
};

class AccessFactory:public IFactory
{
public:
    IUser *createUser()
    {
        return new AccessUser();
    }
    IDepartment *createDepartment() 
    {
        return new AccessDepartment();
    }
};

int main()
{
    IFactory *factory=new SqlFactory();
    IUser *user = user=factory->createUser();
    IDepartment * department = factory->createDepartment();
    
    user->getUser();
    user->setUser();
    department->getDepartment();
    department->setDepartment();

    return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class SqlUser:
    def getUser(self):
        print "在sql中返回user"
    def setUser(self):
        print "在sql中设置user"

class AccessUser:
    def getUser(self):
        print "在Access中返回user"
    def setUser(self):
        print "在Access中设置user"

class SqlDepartment:
    def getDepartment(self):
        print "在sql中返回Department"
    def setDepartment(self):
        print "在sql中设置Department"

class AccessDepartment:
    def getDepartment(self):
        print "在Access中返回Department"
    def setDepartment(self):
        print "在Access中设置Department"

class SqlFactory:
    def createUser(self):
        return SqlUser()
    def createDepartment(self):
        return SqlDepartment()

class AccessFactory:
    def createUser(self):
        return AccessUser()
    def createDepartment(self):
        return AccessDepartment()

if __name__ == '__main__':
    factory = SqlFactory()
    user = factory.createUser()
    department = factory.createDepartment()

    user.getUser()
    user.setUser()
    department.getDepartment()
    department.setDepartment()
```

## Tips:

`Python`实现的抽象工厂模式相比与`C++`来说并没有什么显著特点。`抽象工厂模式`可以结合`简单工厂`加`反射`写出更简洁、灵活的客户端代码，有兴趣的同学自己尝试一下。

`Python`的抽象工厂模式实现比`C++`版本简洁了不少，这得益于`Python`的`鸭子类型`。有关`鸭子类型`详见：《当DP遇见Py(五) -- 工厂方法模式》。


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/o_ch11.抽象工厂模式.png