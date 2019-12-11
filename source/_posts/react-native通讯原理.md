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
#### 1.1 Native调用JS
在RN里，封装了底层细节，外部暴露出的是通过RCTCxxBridge方法enqueueJSCall:method:args:completion调用。该方法实现如下:callFunction方法先执行js端方法callFunctionReturnFlushedQueue(在MessageQueue.js文件中),返回js端消息队列，然后native解析队列，即调用callNativeModules，这个过程在下文JS调用Native有分析。
总体来说还是使用JSCHelpers中封装的C++方法`evaluateScript(JSContextRef, JSStringRef, JSStringRef)`，在常驻线程来执行js语句，返回结果给native解析。


#### 1.2 JS调用Native
​js端也有 有BatchedBridge概念，node_modules/react-native/Libraries/BatchedBridge/BatchedBridge.js中，const BatchedBridge = new MessageQueue();，BatchedBridge对象实际上是MessageQueue的实例，转到当前目录下的MessageQueue.js文件。js需要调用native方法的时候，调用`enqueueNativeCall`函数，比如js端执行方法：enqueueNativeCall向_queue依次插入`moduleID methodID params ,flushedQueue`方法会把当前的_callID插入到_queue最后，**紧接着判断相邻两次flushQueue时间超过MIN_TIME_BETWEEN_FLUSHES_MS即5ms，或者当前没有正在处理的方法，则执行全局nativeFlushQueueImmediate函数**。nativeFlushQueueImmediate函数传入_queue参数，它在native端之前通过installNativeHook注入了。
js端调用后native端收到函数回调，最终对应执行JSCExecutor类的nativeFlushQueueImmediate方法，该方法最终调用到JsToNativeBridge的callNativeModules方法，callNativeModules解析出js透传的参数_queue，然后动态调用方法。

js只是把消息加入了队列，js什么时候去让native去取js的消息队列处理？
* js端超时机制
需要注意的是，远程调试模式并没有超时机制，global.nativeFlushQueueImmediate始终是 undefined的。每次消息入队的时候，会检查距离上次队列清空完成是否超过5ms，超过则调用nativeFlushQueueImmediate 清空队列，native注册回调被调用，否则立即入队，由于js是单线程的，5ms内也不会积压很多消息，所以不用担心处理效率问题。

* native主动调用
native调用js方法，native调用enqueueJSCall:method:args:completion方法会取到js消息队列，

综上所述，js调用native实际上是有两种机制的:
1. native向jscontext的 global注入全局对象，同时注册相应的回调，如nativeFlushQueueImmediate，js函数被调用，对应native回调被响应
2. js组成消息队列，native调用flushedQueue主动去取
第一种是JSPatch所采用的，不过它注册的回调是一个block, 第二种机制是RN采用的，最复杂的，对于模块，需要两端维护一份配置表，但是最高效的，js方需要执行native方法，仅需传递moduleId methodId arguments必要参数给native，而方法真正执行是在native方异步执行的，返回结果异步返回给js方，如果换成方式1，native方法在jscontext同步执行，明显影响效率，而且 当短时间内有很多条消息，JS并不会去频繁调用native，会在5ms内去累积消息，然后发送给native。


https://www.jianshu.com/p/f49b798299b6

