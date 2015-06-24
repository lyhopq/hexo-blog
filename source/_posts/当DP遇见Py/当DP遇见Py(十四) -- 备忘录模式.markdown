title: 当DP遇见Py(十四) -- 备忘录模式
date: 2015-3-28
categories: Python
tags: [Python, C++, DP]

---

## 定义：
> 在不破坏封装性的前提下捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后可以将对象恢复到原先保存的状态。

## 类图：
![][1]

## 类型：行为型

<!-- more -->

## 实例：
> 打游戏一定要存进度

### C++ 实现
```C++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class Memo
{
public:
	string state;
	Memo(string state)
	{
		this->state=state;
	}
};

class Originator
{
public:
	string state;
	void setMemo(Memo *memo)
	{
		state=memo->state;
	}
	Memo *createMemo()
	{
		return new Memo(state);
	}
	void show()
	{
		cout<<state<<endl;
	}
};	

class Caretaker
{
public:
	vector<Memo *> memo;
	void save(Memo *memo)
	{
		(this->memo).push_back(memo);
	}
	Memo *getState(int i)
	{
		return memo[i];
	}
};
 
int main()
{
	Originator *og=new Originator();
	Caretaker *ct=new Caretaker(); 

	og->state="on";
	og->show();	
	ct->save(og->createMemo());

	og->state="off";
	og->show();
	ct->save(og->createMemo());

	og->state="middle";
	og->show();
	ct->save(og->createMemo());

	og->setMemo( ct->getState(2) );
	og->show();

	return 0;
}
```

### Python 实现
```python
class Originator:
    def __init__(self):
        self.state = ""
    def show(self):
        print self.state
    def createMemo(self):
        return Memo(self.state)
    def setMemo(self, memo):
        self.state = memo.state

class Memo:
    state= ""
    def __init__(self, state):
        self.state = state

class Caretaker:
    memos = []
    def save(self, memo):
        self.memos.append(memo)

    def getMemo(self, index):
        return self.memos[index]

if __name__ == "__main__":
    og = Originator()
    ct = Caretaker()

    og.state="on"
    og.show()
    ct.save(og.createMemo())

    og.state="off"
    og.show()
    ct.save(og.createMemo())

    og.state="middle"
    og.show()
    ct.save(og.createMemo())

    og.setMemo(ct.getMemo(2))
    og.show()
```

### 执行结果：
```python
on
off
middle
middle
```

## Tips:

`备忘录模式`的`Python`实现没有什么特点，在这就不在赘述了。

![][2]


  [1]: http://78rbbi.com1.z0.glb.clouddn.com/o_ch14.备忘录模式.png
  [2]: http://news.5068.com/upfiles/allimg/120323/1A421K21-0.jpg
