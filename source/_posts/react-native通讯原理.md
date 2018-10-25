---
title: react-native通讯原理
date: 2017-12-14 23:05:21
tags:
    - react-native
    - 原理
categories: React-Native
---

### 1.RN的通讯原理
<!-- more -->
js调用native实际上是有两种机制的:

native向jscontext的 global注入全局对象，同时注册相应的回调，如nativeFlushQueueImmediate，js函数被调用，对应native回调被响应
js组成消息队列，native调用flushedQueue主动去取

https://www.jianshu.com/p/f49b798299b6

