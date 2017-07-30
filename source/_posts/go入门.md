---
title: go入门
date: 2017-07-30 20:29:53
tags:
  - go
---

## 1.import _

import 下划线（如：import _ hello/imp）的作用：当导入一个包时，该包下的文件里所有init()函数都会被执行，然而，有些时候我们并不需要把整个包都导入进来，仅仅是是希望它执行init()函数而已。这个时候就可以使用 import _ 引用该包。即使用【import _ 包路径】只是引用该包，仅仅是为了调用init()函数，所以无法通过包名来调用包中的其他函数。

## 2.import .
import( . “fmt” ) 这个点操作的含义就是这个包导入之后在你调用这个包的函数时，你可以省略前缀的包名，也就是前面你调用的fmt.Println(“hello world”) 可以省略的写成Println(“hello world”)

## 3.import f
import( f “fmt” ) 别名操作调用包函数时前缀变成了重命名的前缀，即f.Println(“hello world”)

<!-- more -->
