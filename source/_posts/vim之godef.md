title: vim之godef
date: 2013-10-24 09:40:50
categories: [Golang]
tags: [vim,Golang, godef]
---

最近在研究Golang，作为Vimer当然要配置vim来建立Go的工作环境。对于一门语言的初学者来说，学习别人的代码，能够对代码中的符号进行自动跳转是比较重要的。安装godef和相应插件，就可以实现这个功能。

安装步骤
--------

* `go get code.google.com/p/rog-go/exp/cmd/godef`
* `git clone https://github.com/dgryski/vim-godef.git ~/.vim/bundle/vim-godef`

使用方法
---------

在Go代码文件，把光标移到一个函数或变量上，在命令模式下输入`gd`。vim会显示这个函数的定义。详细配置参见[vim-godef](https://github.com/dgryski/vim-godef)。

* * *

`vim-godef`提供了三种跳转方式：

1. 切割窗口

        g:godef_split=1

2. 打开新新窗口

        g:godef_split=2

3. 在文件内的符号步切割窗口

        g:godef_same_file_in_same_window=1

感觉这三种方式都不是很爽，希望要的是：

* 在文件内的符号切割窗口
* 在文件外的符号打开新窗口

小小修改了一下`vim-godef`的插件：

```
if out =~ 'godef: '
    let out=substitute(out, '\n$', '', '')
    echom out
elseif g:godef_same_file_in_same_window == 1 && (out) =~ expand('%:t')
    lexpr out
else
    if g:godef_split == 1
        split
    elseif g:godef_split == 2
        if (out) =~ expand('%:t')
            split
        else
            tabnew
        end
    endif
    lexpr out
end
```
在`~/.vimrc`中添加：

    let g:godef_split=2

搞定！
