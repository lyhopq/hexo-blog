title: C语言结构体对齐算法
date: 2013-10-23 15:20:16
tags: [结构体对齐,算法]

---

最近做了一个关于结构体补齐填充的东西，补了一下关于结构体对齐方面的知识（以前也研究过，不过都忘了，所以在这记录一下）。


首先看一下结构体对齐的三个概念值： 

1	数据类型的`默认对齐值`（自身对齐）：

    1	基本数据类型：为指定平台上基本类型的长度。如在32位机器中，char对齐值为1，short为2，int,float为4，double为8；
    2	结构体：其数据成员中默认对齐值最大的那个值。
    
2	`指定对齐值`：#pragma pack (value)时的指定对齐值value。 
3	数据类型的`有效对齐值`：默认对齐值和指定对齐值中小的那个值。

有了这些值，我们就可以很方便的来讨论具体数据结构的成员和其自身的对齐方式。有效对齐值N是最终用来决定数据存放地址方式的值，最重要。有效对齐N，就是表示“对齐在N上”，也就是说该数据的`偏移量`%N=0”。而数据结构中的数据变量都是按定义的先后顺序来排放的。第一个数据变量的起始地址就是数据结构的起始地址。结构体的成员变量要对齐排放（对于非对齐成员需要在其前面填充一些字节，保证其在对齐位置上），结构体本身也要根据自身的有效对齐值圆整(就是结`构体总长度`需要是结构体有效对齐值的整数倍)。

通过上面的分析，对结构体进行字节对齐，我们需要知道四个值：
1	指定对齐值：代码中指定的对齐值，记为packLen；
2	默认对齐值：结构体中每个数据成员及结构体本身都有默认对齐值，记为defaultLen；
3	成员偏移量：即相对于结构体起始位置的长度，记为offset；
4	成员长度：结构体中每个数据成员的长度（注结构体成员为补齐之后的长度），记为memberLen。

及两个规则：
1	对齐规则：`offset % vaildLen = 0`，其中vaildLen为有效对齐值`vaildLen = min(packLen, defaultLen)`；
2	填充规则：如成员变量不遵守对齐规则，则需要对其补齐；在其前面填充一些字节保证该成员对齐。需填充的字节数记为padLen：

    ```
    padLen = getPadLen(offset , defaultLen);
    
    int getPadLen(int offsetLen, int defaultLen)
    {
        int vaildLen = min(packLen,defaultLen);
        if(0 == vaildLen || 0 == offsetLen % vaildLen)
        {
            return 0;
        }
        return vaildLen - (offsetLen % vaildLen);
    }
    ```
     

结构体对齐算法思想：深度优先填充
1	先对齐内层结构体；
2	对每个数据成员计算其`defaultLen`、`memberLen`和`offset`；
    a	在遍历每个数据成员时计算；
    b	对于基本数据类型成员`defaultLen=memberLen`；对于结构体成员`defaultLen`等于它的所有成员的最大的`memberLen`；
    c	遍历时对成员的`memberLen`进行累加，得到当前成员的`offsetLen`；
3	运用对齐及填充规则：在当前结构体成员前填充padLen个字节；
