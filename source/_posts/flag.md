title: go flag
date: 2013-10-19 15:20:16
categories: Golang 
tags: [GO,命令行解析]

---

flag 是Go 标准库提供的解析命令行参数的包。

## 使用方式：
1. flag.Type(name, defValue, usage)

    其中Type为String, Int, Bool等；并返回一个相应类型的指针。

2. flag.TypeVar(&flagvar, name, defValue, usage)

    将flag绑定到一个变量上。
    
<!-- more -->

## 自定义flag

只要实现flag.Value接口即可：

```
type Value interface {
    String() string
    Set(string) error
}
```
通过如下方式定义该flag：

    flag.Var(&flagvar, name, usage)


## 示例

```
package main

import "flag"
import "fmt"
import "strconv"

type percentage float32
func (p *percentage) Set(s string) error {
    v, err := strconv.ParseFloat(s, 32)
    *p = percentage(v)
    return err
}
func (p *percentage) String() string { return fmt.Sprintf("%f", *p) }

func main() {
    namePtr := flag.String("name", "lyh", "user's name")
    agePtr := flag.Int("age", 22, "user's age")
    vipPtr := flag.Bool("vip", true, "is a vip user")

    var email string
    flag.StringVar(&email, "email", "lyhopq@gmail.com", "user's email")

    var pop percentage
    flag.Var(&pop, "pop", "popularity")

    flag.Parse()
    others := flag.Args()

    fmt.Println("name:", *namePtr)
    fmt.Println("age:", *agePtr)
    fmt.Println("vip:", *vipPtr)
    fmt.Println("pop:", pop)
    fmt.Println("email:", email)
    fmt.Println("other:", others)
}
```

```
$ ./command-line-flags
name: lyh
age: 22
vip: true
email: lyhopq@gmail.com
other: []
```

```
$ ./command-line-flags -name golang -age 4 -vip=true -pop 99 简洁 高并发 等等
name: golang
age: 4
vip: true
pop: 99
email: lyhopq@gmail.com
other: [简洁 高并发 等等]
```

```
$ ./command-line-flags -h
Usage of ./command-line-flags:
  -age=22: user's age
  -email="lyhopq@gmail.com": user's email
  -name="lyh": user's name
  -pop=0.0: popularity
  -vip=true: is a vip user
```




