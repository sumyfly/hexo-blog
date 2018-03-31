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
