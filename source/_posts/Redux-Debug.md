---
title: Redux-Debug
date: 2018-07-24 19:13:25
tags:
---
### 1. dispatch同一个action在极短的时间内2次
如果reducer里面是用的`return fromJS(action.payload)`,然后dispatch同一个action在极短的时间内2次，导致的结果就是第二次使用的immutable state是旧的，它没有被第一次的action所更改，这样的后果就是状态丢失了。JS执行时单线程，所以reudx这里并没有race condition,真正的原因是这两次dispatch是在同一render桢中运行的，所以它们mapStateToProps中原始state是相同的，事实上就是第二次修改覆盖了第一次修改（reducer中使用了fromJS,而非mergeDeep)。

解决方案：
1. 多个action修改不同部分
2. 多个修改合并到一个action
我先选择#2

### 2. yield select 连续多个
好像如果有一个循环，大约100多次，在循环体里面yield select(selctor), 那么可能导致saga不工作，但是redux还是工作的。

<!-- more -->