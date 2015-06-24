title: 当DP遇见Py(六) -- 原型模式
date: 2014-12-28
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

## 类图：
![][1]

## 类型：创建型

<!-- more -->

## 实例：
> 从简历原型，生成新的简历。

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;
 
class Resume
{
private:
	string name,sex,age,timeArea,company;
public:
	Resume(string s)
	{
		name=s;
	}
	void setPersonalInfo(string s,string a)
	{
		sex=s;
		age=a;
	}
	void setWorkExperience(string t,string c)
	{
		timeArea=t;
		company=c;
	}
	void display()
	{
		cout<<name<<"  "<<sex<<"  "<<age<<endl;
		cout<<"工作经历:  "<<timeArea<<"  "<<company<<endl<<endl;

	}
	Resume *clone()
	{
		Resume *b;
		b=new Resume(name);
		b->setPersonalInfo(sex,age);
		b->setWorkExperience(timeArea,company);
		return b;
	}
};


int main()
{
	Resume *a = new Resume("大鸟"); 	 
	a->setPersonalInfo("男", "29");
	a->setWorkExperience("1998-2000", "XX公司");

	Resume *b = a->clone();
	b->setWorkExperience("1998-2006", "YY企业");
	
	Resume *c = a->clone();
	c->setPersonalInfo("女", "24");
	
	a->display();
	b->display();
	c->display();
	
	return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

import copy

class Prototype:
    def __init__(self):
        self._objects = {}

    def register_object(self, name, obj):
        self._objects[name] = obj

    def unregister_object(self, name):
        del self._objects[name]

    def clone(self, name, **attr):
        obj = copy.deepcopy(self._objects[name])
        obj.__dict__.update(attr)
        return obj

class Resume:
    def __init__(self, name):
        self.name = name

    def set_personal_info(self, sex, age):
        self.sex = sex
        self.age = age

    def set_work_experience(self, time_area, company):
        self.time_area = time_area
        self.company = company

    def __str__(self):
        return '%s %s %s\n工作经历：%s %s' % (
            self.name, self.sex, self.age,
            self.time_area, self.company
        )

if __name__ == '__main__':
    a = Resume('大鸟')
    a.set_personal_info('男', 29)
    a.set_work_experience('1998-2000', 'XX公司')

    prototype = Prototype()
    prototype.register_object('resume', a)

    b = prototype.clone('resume')
    b.set_work_experience('1998-2006', 'YY企业')

    c = prototype.clone('resume', sex='女', age=24)

    print a
    print b
    print c
```

### 执行结果：
```python
大鸟 男 29
工作经历：1998-2000 XX公司
大鸟 男 29
工作经历：1998-2006 YY企业
大鸟 女 24
工作经历：1998-2000 XX公司
```

## Tips:
- 以上的`Python`实现比`C++`提供了更丰富和完整的`原型模式`实现。使用`Prototype`类来管理`原型`，并提供统一的`clone`方法来生成新对象。
- Python为对象提供的copy模块中的copy方法和deepcopy方法已经实现了原型模式。`copy`方法实现浅拷贝，`deepcopy`方法实现深拷贝。
- `__str__`魔术方法。用来定义对象如何转换为字符串形式，被`print`所调用。关于`Python`的**魔术方法**请见《当DP遇见Py(四) -- 代理模式》中的介绍。
- `__dict__`。`Python`中的`__dict__`比较神奇，一两句话也说不清楚。简而言之，你可以把`__dict__`看做`Python`对象的属性字典（注：Python中一切皆对象，O(∩_∩)O~）。`Python`的强大的动态能力，如：Python的对象可以在运行期动态添加定义时没有的属性，就是这货在提供支持。语句`obj.__dict__.update(attr)`就是根据用户传入的`attr`来更新对象`obj`的属性。


![][2]![][3]

> “克隆”是要给钱地！


  [1]: http://images.cnblogs.com/cnblogs_com/wuyuegb2312/468244/o_ch6.%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F.png
  [2]: http://img1.mydrivers.com/img/20141205/769b71b924d24aca94f0a32f3e167495.jpg
  [3]: http://img1.mydrivers.com/img/20141205/e1edbc849aaf49b7b9078ea6e9214984.jpg