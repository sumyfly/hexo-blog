---
title: ios-storyboard
date: 2018-10-27 15:37:06
tags:
- ios
---

### 1. Storyboard的Entry Point
选择一个ViewController,在Attributes Inspector中勾选`Is Initial View Controller`。

### 2. Storyboard的导航
有两个导航`Navigation Controller`和`Tab Bar Controller`,这两个在Editor-Embed In->Navigation Controller/Tab Bar Controller.加上去之后，根组件就会变成这两个。`self.navigationController`才会有值，不然是nil。

