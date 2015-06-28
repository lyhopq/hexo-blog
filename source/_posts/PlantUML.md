title: PlantUML
date: 2013-10-21 09:40:50
categories: [UML]
tags: [PlantUML,UML,ArchLinux,shell]
---


[PlantUML](http://plantuml.sourceforge.net
)是一个开源的工具，使用简单的文字描述绘制UML图。它的后端使用的是[Graphivz](http://www.graphviz.org
)，其封装了dot语法，使得能够使用简洁的语法来绘制UML图。

在Archlinux中配置PlantUML工具：

1. 安装依赖软件：

        sudo pacman -S graphivz jre7-openjdk

2. 下载[plantuml.jar](http://sourceforge.net/projects/plantuml/files/plantuml.jar/download)，把他放在一个方便的地方，如：`~/.local/lib`目录下
3. 试用：新建文件`test.uml`，输入：

    ```
    @startuml img/test.png
    class TestA {
        -String name
        +int id
    }
    
    class TestB extends TestA{
       -String desc
       +String getDesc()
       +void setDesc(String desc)
    }
    @enduml
    ```

    注：第一行可以指定生成的图片路径。
    
    然后运行以下命令生成UML类图：
    
        java -jar ~/.local/lib/plantuml.jar -tpng test.uml
    
    ![UML类图示例](http://78rbbi.com1.z0.glb.clouddn.com/test.png)
    
4. 为了使用方便，写了一个shell脚本

    ```
    #!/usr/bin/bash
    
    UML=$1
    FILE=`head -n1 $UML | awk '{print $2}'`
    if [ -n "$FILE" ]; then
        EXT=${FILE##*.}
        java -jar ~/.local/lib/plantuml.jar -t$EXT $@
        echo $FILE
    else
        java -jar ~/.local/lib/plantuml.jar $@
        echo ${UML%.*}.png
    fi
    ```
    
    主要用来封装PlantUML的调用，并且更具输入文件第一行判断生成文件类型。
    
    使用方式：
    
        ./uml test.uml
   

下面举一个比较复杂的例子：

```
@startuml img/ticket.svg

title 售票机控制程序

scale 1500 width
Component <|-- Keyboard : 继承关系
Component <|-- Screen
Component <|-- CardDriver
Component <|-- CashSlot
Component <|-- Printer
Keyboard <|-- ActionKeyboard
Keyboard <|-- TicketKindKeyboard
Keyboard <|-- DestinationKeyboard

ActionKeyboard <--* TicketSoldSystem : 组合关系
TicketKindKeyboard <--* TicketSoldSystem
DestinationKeyboard <--* TicketSoldSystem
Screen <--* TicketSoldSystem
CardDriver <--* TicketSoldSystem
CashSlot <--* TicketSoldSystem
Printer <--* TicketSoldSystem

skinparam classAttributeIconSize 0

note top of Component : 抽象部件类，所有部件类的父类
note left of Keyboard : 键盘抽象类
class Component {
     +init():void
      +doSelfTest():void
}
class Keyboard {
     +getSelectedKey():int
}

class Screen {
     +showText():void
}
class CardDriver {
     +getCredit():String
      +debitFare():double
       +ejectMCard():void
}
class CashSlot {
     +getCash():String
}
class Printer {
     +printTicket():void
      +ejectTicket():void
}
class ActionKeyboard {
     +getAction():int
}
class TicketKindKeyboard {
     +getTicketKind():String
}
class DestinationKeyboard {
     +getDestinationCode():String
}
class TicketSoldSystem {
     +verifyCredit():boolean
      +calculateFare():double
}

note as Comment
  <color:royalBlue>(1) 目的地键盘用来输入行程目的地的代码（例如，200表示总站）。</color>
  (2) 乘客可以通过车票键盘选择车票种类（单程票、多次往返票和座席种类）。
  (3) 继续/取消键盘上的取消按钮用于取消购票过程，继续按钮允许乘客连续购买多张票。
  (4) 显示屏显示所有的系统输出和用户提示信息。
  (5) 插卡口接受MCard（现金卡），硬币口和纸币槽接受现金。
  (6) 打印机用于输出车票。
  (7) 所有部件均可实现自检并恢复到初始状态。
end note

@enduml
```

<img src="http://78rbbi.com1.z0.glb.clouddn.com/ticket.png" alt="" width="80%">


[1]: http://plantuml.sourceforge.net
[2]: http://www.graphviz.org
[3]: http://sourceforge.net/projects/plantuml/files/plantuml.jar/download
