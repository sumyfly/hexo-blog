---
title: Redux-Debug
date: 2018-07-24 19:13:25
tags:
---
### 1. dispatch同一个action在极短的时间内2次
如果reducer里面是用的`return fromJS(action.payload)`,然后dispatch同一个action在极短的时间内2次，导致的结果就是第二次使用的immutable state是旧的，它没有被第一次的action所更改，这样的后果就是状态丢失了。TODO: check redux source code later.

解决方案：
1. 多个action修改不同部分
2. 多个修改合并到一个action
我先选择#2
<!-- more -->