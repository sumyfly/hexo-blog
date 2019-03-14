---
title: go入门
date: 2017-07-30 20:29:53
tags:
  - go
---

### 1.import _

import 下划线（如：import _ hello/imp）的作用：当导入一个包时，该包下的文件里所有init()函数都会被执行，然而，有些时候我们并不需要把整个包都导入进来，仅仅是是希望它执行init()函数而已。这个时候就可以使用 import _ 引用该包。即使用【import _ 包路径】只是引用该包，仅仅是为了调用init()函数，所以无法通过包名来调用包中的其他函数。

### 2.import .
import( . “fmt” ) 这个点操作的含义就是这个包导入之后在你调用这个包的函数时，你可以省略前缀的包名，也就是前面你调用的fmt.Println(“hello world”) 可以省略的写成Println(“hello world”)

### 3.import f
import( f “fmt” ) 别名操作调用包函数时前缀变成了重命名的前缀，即f.Println(“hello world”)

<!-- more -->

### 4.CSP vs Actor
Golang在并发设计方面参考了C.A.R Hoare的CSP，即Communicating Sequential Processes并发模型理论。

Actor模型广义上讲与CSP模型很相似。但两种模型就提供的原语而言，又有一些根本上的不同之处：
>- CSP模型处理过程是匿名的，而Actor模型中的Actor则具有身份标识。
- CSP模型的消息传递在收发消息进程间包含了一个交会点，即发送方只能在接收方准备好接收消息时才能发送消息。相反，actor模型中的消息传递是异步 的，即消息的发送和接收无需在同一时间进行，发送方可以在接收方准备好接收消息前将消息发送出去。这两种方案可以认为是彼此对偶的。在某种意义下，基于交 会点的系统可以通过构造带缓冲的通信的方式来模拟异步消息系统。而异步系统可以通过构造带消息/应答协议的方式来同步发送方和接收方来模拟交会点似的通信 方式。
- CSP使用显式的Channel用于消息传递，而Actor模型则将消息发送给命名的目的Actor。这两种方法可以被认为是对偶的。某种意义下，进程可 以从一个实际上拥有身份标识的channel接收消息，而通过将actors构造成类Channel的行为模式也可以打破actors之间的名字耦合。

### 5. visual code中go extension的调试功能
需要手动安装delve,MacOS需要下载源码，然后`make install`。然后就可以打断点调试了。
https://github.com/derekparker/delve/blob/master/Documentation/installation/osx/install.md

### 6. $GOPATH 多个目录
设置了GOPATH 之后，将 `$GOPATH/bin` 加入 PATH 中，这样可以方便的运行 go install 好的二进制程序。然而，当存在 GOPATH 中存在多个路径时，这种写法只会将最后一个路径跟上bin。在mac或linux下可以通过这种方式解决：
`${GOPATH//://bin:}/bin`

https://www.cnblogs.com/52php/p/6246858.html

### 7. go的package引用路径
go的package都是从`$GOPATH`下面一个目录一个目录的找到，不可以使用相对路径。（如果项目在`$GOPATH`之外，可以使用。？）

### 8. go package manager
之前go都是在$GOPATH下安装包，所有的包安装在一个统一目录下，有方便的地方，也有不方便的地方。方便的地方是引用时简单，就像一个Global环境。但是不方便的是包的版本冲突，go 1.5之后有了vendor，依赖的包就可以放到项目目录下面的vendor文件夹中，每个项目的依赖都是独立的。和node的npm, python的virtualenv、pipenv都是一个路子了，看来依赖管理还是必须是本地依赖才最优。

#### 8.1 glide
下载 `go get github.com/Masterminds/glide` 
安装 `go install github.com/Masterminds/glide`
初始化 新建项目录，然后进去`glide init`,产生glide.yaml
使用 `glide get github.com/bitly/go-simplejson`,然后glide.yaml中package中有这条记录。
安装包 `glide install`
更新 `glide update | up`
列表 `glide list`， 这个命令我执行失败了。

https://github.com/Masterminds/glide

#### 8.2 dep
目前安装有点麻烦，我使用的是源码。要求go版本大于1.9。
https://golang.github.io/dep/docs/installation.html


初始化 `dep init`
安装 `dep ensure`
安装新包 `dep ensure -add github.com/dropbox/godropbox/time2`,这里有个限制条件，必须此目录下面有go文件，不然安装不了。go get也是要求远程目录下有go文件。
查看安装信息 `dep status`

> 对比一下，dep更加易用，它使用分支。glide也推荐使用dep.

### 9. defer
当defer关键字声明的代码不止一行的时候，就引入了defer下压栈的特性，这也是Go比较强大的地方，根据下压栈的特点，后压入的那行代码会在上层函数执行完毕后先执行。
``` go
package main

import "fmt"

func main() {
    fmt.Println("counting")

    for i := 0; i < 10; i++ {
        defer fmt.Println(i)
    }

    fmt.Println("done")
}
// 输出 counting, done, 9 , 8 ... 0
```
https://www.cnblogs.com/Evsward/p/go.html

### 10 Install go
#### 10.1 使用brew

``` bash
brew update
brew install golang

# This is actually your .bashrc file
export GOPATH=$HOME/go-workspace # don't forget to change your path correctly!
export GOROOT=/usr/local/opt/go/libexec
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin
```

> $GOROOT是放标准库的，$GOPATH是放非标准库的。所以这两个目录不能一样，如果两个库中都有fmt，那么compiler不知道选择哪一个。

> 还有就是go官方推荐使用一个gopath目录，然后使用vendor方式来管理各个项目的依赖。当然，这个有不同的实践，有些喜欢一个项目一个gopath，很少同一个项目使用多个gopath目录的。源代码是放在gopath的src目录下的。

#### 10.2 下载tar
> 注意下载正确的版本，linux和mac的编译版本不同，下载了错误的版本，会报`Exec format error`错误。
https://golang.org/dl/

#### 10.3 pkg安装包
和普通application一样的安装步骤，不推荐。
