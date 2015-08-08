title: 当DP遇见Py(十七) -- 单例模式
toc: false
mathjax: false
date: 2015-08-08 13:17:59
categories: Python
tags: [Python, C++, DP]

---

## 定义：

> 保证类仅有一个实例，并提供一个访问它的全局访问点。

## 类图：
![][1]

<!-- more -->

## 类型：创建型

### C++ 实现

```C++
#include <iostream>
#include <string>
using namespace std;

class Singleton
{	
private:
	static Singleton *instance;
	Singleton()
	{
	}
public:
	static Singleton *getInstance()
	{
        if(NULL == instance)
        {
            instance = new Singleton();
        }
        return instance;
	}
};
Singleton* Singleton::instance=NULL; 

class A:public Singleton
{

};

int main()
{
	Singleton *one=Singleton::getInstance();
	Singleton *two=A::getInstance();
	cout<<(one==tow)<<endl;
	return 0;
}
```

### Python 实现

```python
class Singleton(object):
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kw)
        return cls._instance

class MyClass(Singleton):
    pass

if __name__ == '__main__':
    one = MyClass()
    two = MyClass()

    print id(one) == id(two)
```

### Pythonic 实现

```python
def singleton(cls, *args, **kw):
    instances = {}
    
    def _singleton():
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return _singleton

@singleton
class MyClass(object):
    pass

if __name__ == '__main__':
    one = MyClass()
    two = MyClass()

    print id(one) == id(two)
```

## Tips:

`单例模式`是比较简单的设计模式，但在`Python`中其实现方式有多种，详见http://blog.csdn.net/ghostfromheaven/article/details/7671853。

本文例举的第二种实现方法使用了`Python`的`装饰器`，其实现更更elegant和pythonic。`装饰器`在前面的模式中已经介绍过了，在这就不赘述了。

写了这么多，其实`Python`根本不需要`单例模式`，为什么呢？

因为，`Python`的模块（module），最pythonic的单例典范。
> 模块：
>  - 所有的变量都会绑定到模块
>  - 模块只初始化一次
>  - import机制是线程安全的（保证了在并发状态下模块也只有一个实例）

因此，模块在在一个应用程序中只有一份，它本身就是单例的，将你所需要的属性和方法，直接暴露在模块中变成模块的全局变量和方法即可！


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F
