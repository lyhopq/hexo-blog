title: 当DP遇见Py(七) -- 模板方法模式
date: 2015-1-17
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以在不改变一个算法的结构的情况下重新定义该算法的某些特定步骤。

## 类图：
![][1]

## 类型：行为型

<!-- more -->

## 实例：
> 金庸小说考题试卷。

### C++ 实现
```C++
#include <iostream>
#include <string>
using namespace std;

class TestPaper
{
public:
    void question1()
    {
        cout << "练成倚天剑和屠龙刀的玄铁可能是[] A.球墨铸铁 B.马口铁 C.高速合金钢 D.碳素纤维"  <<endl;
        cout << "答案：" << answer1() << endl;
    }
    
    void question2()
    {
        cout << "杨过、程英、陆无双铲除了情花，造成[] A.使这种植物不再害人 B.使一种珍稀物种灭绝 C.破坏生态平衡 D.造成该地区沙漠化" << endl;
        cout << "答案：" << answer2() << endl;
    }
    
    virtual string answer1()
    {
        return "";
    }
    
    virtual string answer2()
    {
        return "";
    }
    
    virtual ~TestPaper()
    {
    }
};

// 学生甲 
class TestPaperA:public TestPaper
{
public:
    string answer1()
    {
        return "A";
    }
    virtual string answer2()
    {
        return "B";
    }
};

// 学生乙
class TestPaperB:public TestPaper
{
public:
    string answer1()
    {
        return "B";
    }
    virtual string answer2()
    {
        return "D";
    }
};


int main()
{
    cout<<"学生甲抄的的试卷:"<<endl;
    TestPaperA s1;
    s1.question1();
    s1.question2();
    
    cout << endl;
    
    cout<<"学生乙抄的的试卷:"<<endl;
    TestPaperB s2;
    s2.question1();
    s2.question2();
    
    return 0;
}
```

### Python 实现
```python
# -*- coding=utf-8 -*-

class TestPaper:
    def TestQuestion1(self):
        print u'练成倚天剑和屠龙刀的玄铁可能是[] A.球墨铸铁 B.马口铁 C.高速合金钢 D.碳素纤维'
        print u'答案： ' + self.Answer1()

    def TestQuestion2(self):
        print u'杨过、程英、陆无双铲除了情花，造成[] A.使这种植物不再害人 B.使一种珍稀物种灭绝 C.破坏生态平衡 D.造成该地区沙漠化'
        print u'答案： ' + self.Answer2()

    def Answer1(self):
        return ''

    def Answer2(self):
        return ''

#学生甲
class TestPaperA(TestPaper):
    def Answer1(self):
        return 'A'

    def Answer2(self):
        return 'B'

#学生乙
class TestPaperB(TestPaper):
    def Answer1(self):
        return 'B'

    def Answer2(self):
        return 'D'

if __name__ == '__main__':
    a = TestPaperA()
    a.TestQuestion1()
    a.TestQuestion2()

    print

    b = TestPaperB()
    b.TestQuestion1()
    b.TestQuestion2()
```

### 执行结果：
```python
练成倚天剑和屠龙刀的玄铁可能是[] A.球墨铸铁 B.马口铁 C.高速合金钢 D.碳素纤维
答案： A
杨过、程英、陆无双铲除了情花，造成[] A.使这种植物不再害人 B.使一种珍稀物种灭绝 C.破坏生态平衡 D.造成该地区沙漠化
答案： B

练成倚天剑和屠龙刀的玄铁可能是[] A.球墨铸铁 B.马口铁 C.高速合金钢 D.碳素纤维
答案： B
杨过、程英、陆无双铲除了情花，造成[] A.使这种植物不再害人 B.使一种珍稀物种灭绝 C.破坏生态平衡 D.造成该地区沙漠化
答案： D
```

## Tips:
`模板方法`的`Python`实现没有什么特点，在这就不在赘述了。


![][2]

> 无处不“模板”！


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/o_ch7.模板方法模式.png
  [2]: http://img5.imgtn.bdimg.com/it/u=581775908,2307964083&fm=21&gp=0.jpg