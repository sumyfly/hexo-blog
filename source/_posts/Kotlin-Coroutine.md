---
title: Kotlin-Coroutine
date: 2020-08-20 10:59:57
tags:
---

### 1. 协程
协程就是轻量级线程，或者称绿色线程，跟线程相似可以并发执行，不同点是：
- 线程由操作系统调度，协程由协程调度器调度。
- 线程有单独的线程栈，协程按照语言设计: Golang有协程栈，支持动态扩展协程栈大小；Tokio没有协程栈，使用的是状态机和闭包保存状态（Kotlin类似?）。
> 协程最重要的地方就是它们可以挂起和恢复，所以表现出并发的能力，它是由调度器提供的。

<!-- more -->

### 2. runBlocking/coroutineScope
- 两者都会阻塞当前协程，等待子协程域完成
- runBlocking会阻塞当前线程，coroutineScope不会阻塞当前线程
- runBlocking可以在任意地方调用，coroutineScope只能在协助中调用
- runBlocking只用于测试，不用于生产代码


### 3. 协程作用域
协程作用域就是协程执行的环境，协程可以在其内部挂起和恢复，同一个协程作用域中的所有非创建子协程作用域的协程都受同一个调度器控制，它们之间执行是同步的。
TODO：同步协程作用域和异步协程作用域的区别？

#### 3.1 最外层协程域
任意地方可以调用
- GlobalScope.launch，即使多个地方调用它也是并发执行的。
- runBlocking，阻塞当前线程。
- MainScope.launch, 主线程的协程作用域。
- CoroutineScope 自定义协程作用域。其实GlobalScope.launch，runBlocking，这些生产中都不常用，应该使用CoroutineScope函数创建协程。
  ``` kotlin
  val job = Job() // job可以cancel其相关作用域中的所有协程
  val scope = CoroutineScope(job)
  scope.lauch {
    // run coroutine
  }
  ```

#### 3.2 创建子协程域
只能在协程域中调用，继承外部协程作用域并且创建一个子协程作用域。子协程域特点是如果外部协程域结束了，该作用域下的所有子协程也会结束了。

- coroutineScope （suspend function)
同步等待子协程域完成

- withContext （suspend function)
同步等待子协程域完成，这个有点特殊，虽然在子协程域中，但是可以指派线程。也就是涉及到调度器之间的交互。

- launch (普通函数)
异步执行子协程域， 返回Job对象，不返回执行结果

- async （普通函数）
异步执行子协程域，返回Deferred对象，调用.await可以返回执行结果

### 4. 执行协程
#### 4.1 不需要执行结果
- 同步：
  * suspend函数不返回结果
- 异步：
  * launch，返回Job对象
  * async+不调用await，返回Deferred对象

#### 4.2 需要执行结果
- 同步：
  * suspend函数返回结果
  * asyn + 同时调用await
  * withContext

- 异步：
  * async + await滞后调用


### 5. suspend
> suspend本身没有协程域，可以返回结果或者Unit

suspend函数 可以返回结果
launch 可以返回结果
async 返回Deferred,在上面调用.await可以取得结果
suspendCoroutine 可以返结果
coroutineScope 可以返回结果

### 6. suspend->resume
协程的挂起由调度器控制，它可以在满足唤醒条件后被resume。
调度器内部使用了状态机来控制状态。
挂起函数会在当前协程作用域挂起，直至其恢复，在其恢复之前切换到其它协程运行，所以看起来就像是同步等待行为。

### 7. 调度器
协程的调度器是全局的吗，还是每个线程一个调度器？看资料说是一个线程一个。那么调度器看起来应该是以最外层的协程作用域为单位。那么Dispatchers.IO怎么说？

