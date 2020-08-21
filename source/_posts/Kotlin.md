---
title: Kotlin
date: 2018-07-10 23:45:17
tags:
---

### 1. ? !! !
val str:String! = ""  //无法声明，临时变量，说明这是Kotlin调用Java代码产生的，如果要使用，最好用可空类型
val str:String? = ""  //可空类型
val str:String!! = "" //1.强制转换；2.不能为空

<!-- more -->

### 2. .javaClass vs ::class.java
The difference in these two constructs is that, for an expression foo of static (declared or inferred) type Foo:

`foo.javaClass` is typed as `Class<Foo>`

`foo::class.java` is typed as `Class<out Foo>`

In fact, the latter is more precise, because the actual value that foo evaluates to can be an instance of not Foo itself but one of its subtypes (and it's exactly what's denoted by the covariant out Foo).

As @marstran correctly noted in the comment on the question, .javaClass once was considered to be deprecated (see the Kotlin 1.1 RC announcement) because it can break type safety (see below), but it was afterwards left as-is because it was widely used and replacing it with the alternative of ::class.java would require adding explicit unchecked casts in the code.

https://stackoverflow.com/questions/46674787/instanceclass-java-vs-instance-javaclass#:~:text=javaClass%20does%20not%20denote%20the,reference%20and%20denotes%20the%20type.

### 3. return@label
``` kotlin
fun foo(ints: List<Int>) {
    ints.forEach {
        if (it == 0) return@forEach // implicit label for lambda passed to forEach
        print(it)
    }
}
```
https://stackoverflow.com/questions/40160489/kotlin-whats-does-return-mean