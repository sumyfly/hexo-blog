---
title: OC的使用
date: 2018-01-04 22:18:50
tags:
  - ios
  - Objective-C
---

### 1.Core Foundation
Core Foundation框架 (CoreFoundation.framework) 是一组C语言接口，它们为iOS应用程序提供基本数据管理和服务功能。

> CF是由C语言实现的，而不是Objective-C，所以如果用到了CF，就需要手动管理内存，ARC是无能为力的。当然因为CF和Foundation之间的友好关系，它们之间的管理权也是可以移交的，这个后面再说。

> Objective-C是ARC管理的对象，而Core Foundation不是ARC管理的对象，所以才要特意这样转换，这与id类型向void*转换是一个概念。也就是说，当这两种类型（有ARC管理，没有ARC管理）在转换时，需要告诉编译器怎样处理对象的所有权。

### 1.1 CF和OC转换关键字
* __bridge
被代入对象的所有者需要明确对象生命周期的管理，不要出现异常访问的问题。OC<->CF
* __bridge_retained
类型被转换时，其对象的所有权也将被变换后变量所持有。 OC->CF
* __bridge_transfer
把本来拥有对象所有权的变量，在类型转换后，让其释放原先所有权。 CF->OC

> __bridge_retained 是编译器替我们做了 retain 操作，而 __bridge_transfer 是替我们做了 release。

[Core Foundation框架详解](https://www.jianshu.com/p/5c98ac2dab58)
<!--more -->
