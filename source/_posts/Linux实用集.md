title: Linux实用集
toc: false
mathjax: false
date: 2013-06-18 14:33:33
categories: Linux
tags: [command,shell,Linux]
toc: true
---



Linux下实现合并PDF文件
--------------------

使用Gost Script和 PDFtk运行如下命令：

```bash
#gs -q -dNOPAUSE -sDEVICE=pdfwrite -sOUTPUTFILE=Linuxidc.pdf -dBATCH *.pdf
```
    
合并结果：把当前目录下所有的 pdf 文件全部合并到 Linuxidc.pdf 中。

    

杀掉僵尸
-------

```bash
$ps -ef | grep defunct    # 列出僵尸(defunct)进程
$kill -9 ppid     # 杀掉僵尸进程的父进程(ppid)
```
     
<!-- more -->

     
查看 TCP/IP 链接状况
------------------

```bash
netstat -n | awk '/^tcp/{++S[$NF]} END {for(a in S) print a, S[a]}'
```
    

系统备份与还原
------------

- 备份

```bash
#cd /
#tar -cvpzf /home/lyh/Backup/xxx.tgz / 
     --exclude=/media --exclude=/proc --exclude=/mnt 
     --exclude=/lost+found --exclude=/tmp 
     --exclude=/sys --exclude=/home
```

- 还原

```bash
#tar -xvzf /home/lyh/xxx.tgz -C /
```


合并文本行
---------

* 合并相邻两行（奇偶合并）

```bash
awk '{if(NR%2==0) {print $0} else {printf $0 "   "}}' filename 
```
或
```
sed '{N;s/\n/   /}' filename
```


smplayer双字幕制作
----------------

```bash
ls -1 *.srt > tmpg
while read l1 && read l2;dog
    file=${l1%.*.*}.srt  # 模式匹配g
    echo $fileg
    cat $l1 $l2 > $fileg
done < tmpg
```
    
模式匹配运算符号：
   * ${var#pattern}最短匹配开头处，并删除该部分。
   * ${var##pattern}最长匹配开头处，并删除该部分。
   * ${var%pattern}最短匹配结尾处，并删除该部分。
   * ${var%%pattern}最长匹配结尾处，并删除该部分。
    

制作ISO文件，并刻录
------------------

```bash
$mkisofs -r -o myISOFile.ISO folderOrFilename
$cdrecord --devic=cdwriter-device -tao -eject myISOFile.ISO
```


远程登录
-------

rdesktop
```
rdesktop -f -r sound:local -r clipboard:PRIMARYCLIPBOARD 
    -r disk:MyDisk=/home/lyh/Downloads -a 24 
    -u administrator -p 203 192.168.0.1
```

X
```bash
X :1.0 -query 192.168.0.1
```


查看系统中文字体
-------------------

```bash
$fc-list :lang=zh-cn
```

Ubuntu 系统备份
-----------------

* 备份

```bash
cd /
sudo tar -cvpzf /home/lyh/Backup/Ubuntu_2009.3.7.tgz / 
    --exclude=/media --exclude=/proc --exclude=/mnt 
    --exclude=/lost+found --exclude=/tmp 
    --exclude=/sys --exclude=/home
```

* 恢复

```bash
tar -xvzf /home/lyh/Backup/Ubuntu_2009.3.7.tgz -C /
```

* 得到已安装软件列表文件 

```bash  
dpkg -–get-selections | grep -v deinstall > ubuntu.files
```

* 从备份的安装包的列表文件恢复所有包 
  
```bash
dpkg --set-selections < ubuntu.files;sudo dselect
```



* 将列表文件发到邮箱中

```bash
dpkg -–get-selections | grep -v deinstall > ubuntu-files 
cat ubuntu-files | mailx -s "ubuntu-files" 自己的email地址
```

文件分割
---------

* 分割

```bash
$ split –b500m myBigFile mySmallFIles.
```

* 合并

```bash
$ cat mySmallFiles.* > myBigFile
```


转换jpg图片为pdf
---------------

```
$ find *.jpg -exec convert {} {}.pdf \;
```

vim去掉 `^M`
-------------------

在vim中显示的 `^M`` 其实是 ``\r`` ，因此使用如下命令去掉： 

```
:%s/\r/g
```


编码转换
-------------

查看文件编码
```bash
enca filename 
```

把文件名由utf-8转到gbk
```bash
convmv -f UTF-8 -t GBK --notest filename
```
把文件内容转换为utf-8
```bash
enca -x utf-8 filename
```
把文件内容由gbk转到utf-8
```bash
iconv -f GBK -t UTF-8 file1 -o file2
```

将当前目录下的文件由GBK编码转换为UTF-8编码，目录结构不变，转码后的文件保存在utf/default目录下
```bash
find . -type f -exec iconv -f GBK -t UTF-8 {} -o utf/{} \;  
```

> 说明：
>  -exec 参数后面跟的是command命令，注意点如下：
>     command命令的终止，使用 ';' (分号）来判定，在后面必须有一个 ';'。特别强调，对于不同的系统，直接使用分号可能会有不同的意义，使用转义符'\'在分号前明确说明。
>     使用{}来表示文件名，也就是find前面处理过程中过滤出来的文件，用于command命令进行处理。

