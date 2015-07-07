title: 当DP遇见Py(十四) -- 备忘录模式
date: 2015-7-4
categories: Python
tags: [Python,DP,C++]
toc: false
---

## 定义：

> 将对象组合成树形结构以表示“部分-整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

## 类图：

![](http://78rbbi.com1.z0.glb.clouddn.com/组合模式.png)

## 类型：结构型

## 实例：

> 公司的组织结构

<!-- more -->

### C++ 实现

```C++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Company
{
public:
    Company(string name)
    {
        this->name=name;
    }
    virtual void add(Company*)=0;
    virtual void remove(Company*)=0;
    virtual void display(int)=0;
    virtual void lineOfDuty()=0;
protected:
    string name;
};

// 人力资源部
class HRDepartment:public Company
{
public:
    HRDepartment(string name):Company(name)
    {}
    void add(Company *c)
    {
    }
    void remove(Company *c)
    {
    }
    void display(int depth)
    {
        string str(depth,'-');
        str+=name;
        cout<<str<<endl;
    }
    void lineOfDuty()
    {
        cout<<name << " 员工培训管理" <<endl;
    }
};

// 财务部
class FinanceDepartment:public Company
{
public:
    FinanceDepartment(string name):Company(name)
    {}
    void add(Company *c)
    {
    }
    void remove(Company *c)
    {
    }
    void display(int depth)
    {
        string str(depth,'-');
        str+=name;
        cout<<str<<endl;
    }
    void lineOfDuty()
    {
        cout<<name << " 公司财务收支管理" <<endl;
    }
};

class ConcreteCompany:public Company
{
public:
    ConcreteCompany(string name):Company(name)
    {}
    void add(Company *c)
    {
        children.push_back(c);
    }
    void remove(Company *c)
    {
        vector<Company*>::iterator iter=children.begin();
        while(iter!=children.end())
        {
            if(*iter==c)
            {
                children.erase(iter);
                break;
            }
            iter++;
        }
    }
    void display(int depth)
    {
        string str(depth,'-');
        str+=name;
        cout<<str<<endl;

        for(vector<Company*>::iterator iter=children.begin(); iter != children.end(); ++iter)
        {
            (*iter)->display(depth+2);
        }
    }

    void lineOfDuty()
    {
        for(vector<Company*>::iterator iter=children.begin(); iter != children.end(); ++iter)
        {
            (*iter)->lineOfDuty();
        }
    }
private:
    vector<Company*> children;
};


int main()
{
    ConcreteCompany *root = new ConcreteCompany("北京总公司");
    root->add(new HRDepartment("总公司人力资源部"));
    root->add(new FinanceDepartment("总公司财务部"));

    ConcreteCompany *comp = new ConcreteCompany("上海华东分公司");
    comp->add(new HRDepartment("华东分公司人力资源部"));
    comp->add(new FinanceDepartment("华东分公司财务部"));
    root->add(comp);

    ConcreteCompany *comp1 = new ConcreteCompany("南京办事处");
    comp1->add(new HRDepartment("南京办事处人力资源部"));
    comp1->add(new FinanceDepartment("南京办事处财务部"));
    comp->add(comp1);

    ConcreteCompany *comp2 = new ConcreteCompany("杭州办事处");
    comp2->add(new HRDepartment("杭州办事处人力资源部"));
    comp2->add(new FinanceDepartment("杭州办事处财务部"));
    comp->add(comp2);

    cout<< "\n结构图： " <<endl;
    root->display(1);

    cout<< "\n职责： " <<endl;
    root->lineOfDuty();

    return 0;
}
```

### Python 实现

```python
# -*- coding=utf-8 -*-

class Company(object):
    def __init__(self, name):
        self.name = name

    def display(depth):
        pass

    def lineOfDuty():
        pass


# 人力资源部
class HRDepartment(Company):
    def __init__(self, name):
        super(HRDepartment, self).__init__(name)

    def display(self, depth):
        print '-' * depth + self.name

    def lineOfDuty(self):
        print '{} 员工培训管理'.format(self.name)


# 财务部
class FinanceDepartment(Company):
    def __init__(self, name):
        super(FinanceDepartment, self).__init__(name)

    def display(self, depth):
        print '-' * depth + self.name

    def lineOfDuty(self):
        print '{} 公司财务收支管理'.format(self.name)


class ConcreteCompany(Company):
    def __init__(self, name):
        self._children = []
        super(ConcreteCompany, self).__init__(name)

    def add(self, c):
        self._children.append(c)

    def remove(self, c):
        self._children.remove(c)

    def display(self, depth):
        print '-' * depth + self.name

        for item in self._children:
            item.display(depth + 2)

    def lineOfDuty(self):
        for item in self._children:
            item.lineOfDuty()


if __name__ == '__main__':
    root = ConcreteCompany("北京总公司")
    root.add(HRDepartment("总公司人力资源部"))
    root.add(FinanceDepartment("总公司财务部"))

    comp = ConcreteCompany("上海华东分公司")
    comp.add(HRDepartment("华东分公司人力资源部"))
    comp.add(FinanceDepartment("华东分公司财务部"))
    root.add(comp)

    comp1 = ConcreteCompany("南京办事处")
    comp1.add(HRDepartment("南京办事处人力资源部"))
    comp1.add(FinanceDepartment("南京办事处财务部"))
    comp.add(comp1)

    comp2 = ConcreteCompany("杭州办事处")
    comp2.add(HRDepartment("杭州办事处人力资源部"))
    comp2.add(FinanceDepartment("杭州办事处财务部"))
    comp.add(comp2)

    print '\n结构图：'
    root.display(1)

    print '\n职责：'
    root.lineOfDuty()
```

### 执行结果：

```python
结构图：
-北京总公司
---总公司人力资源部
---总公司财务部
---上海华东分公司
-----华东分公司人力资源部
-----华东分公司财务部
-----南京办事处
-------南京办事处人力资源部
-------南京办事处财务部
-----杭州办事处
-------杭州办事处人力资源部
-------杭州办事处财务部

职责：
总公司人力资源部 员工培训管理
总公司财务部 公司财务收支管理
华东分公司人力资源部 员工培训管理
华东分公司财务部 公司财务收支管理
南京办事处人力资源部 员工培训管理
南京办事处财务部 公司财务收支管理
杭州办事处人力资源部 员工培训管理
杭州办事处财务部 公司财务收支管理
```

## Tips:

`组合模式`的`Python`实现没有什么特点，在这就不在赘述了。

![](http://78rbbi.com1.z0.glb.clouddn.com/55e74f8862f4eeb76b9d0b6086b02d9e.jpg)


