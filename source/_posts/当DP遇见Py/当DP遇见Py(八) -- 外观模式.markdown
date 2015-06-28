title: 当DP遇见Py(八) -- 外观模式
date: 2015-1-31
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

## 类图：
![][1]

## 类型：结构型

<!-- more -->

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class Sub1
{
public:
	void f1()
	{
		cout<<"子系统的方法 1"<<endl;
	}
};

class Sub2
{
public:
	void f2()
	{
		cout<<"子系统的方法 2"<<endl;
	}
};

class Sub3
{
public:
	void f3()
	{
		cout<<"子系统的方法 3"<<endl;
	}
};

class Facade
{
private:
	Sub1 *s1;
	Sub2 *s2;
	Sub3 *s3;
public:
	Facade()
	{
		s1=new Sub1();
		s2=new Sub2();
		s3=new Sub3();
	}
	
	void method()
	{
		s1->f1();
		s2->f2();
		s3->f3();
	}
};

int main()
{
	Facade *f=new Facade();
	f->method();s
	return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class Sub1:
    def f1(self):
        print u"子系统的方法 1"

class Sub2:
    def f2(self):
        print u"子系统的方法 2"

class Sub3:
    def f3(self):
        print u"子系统的方法 3"

class Facade:
    def __init__(self):
        self.s1 = Sub1()
        self.s2 = Sub2()
        self.s3 = Sub3()

    def method(self):
        self.s1.f1()
        self.s2.f2()
        self.s3.f3()

if __name__ == "__main__":
    facade = Facade()
    facade.method()
```

### 执行结果：
```python
子系统的方法 1
子系统的方法 2
子系统的方法 3
```

## Tips:
`外观模式`的`Python`实现没有什么特点，在这就不在赘述了。

![][2]
![][3]


[1]: http://78rbbi.com1.z0.glb.clouddn.com/外观模式.png
[2]: http://zj.sinaimg.cn/2012/0713/S64582T1342155984801.jpg
[3]: http://www.pp3.cn/uploads/allimg/111215/1116342X6-3.jpg
