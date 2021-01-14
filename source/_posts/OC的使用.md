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

### 1.2 Cocoa Foundation
Foundation框架定义的是OC类的基础层,它的根是NSObject类，（和NSObject及NSCopying协议一起）定义了基本的对象属性和行为。除了提供一组有用的基本对象类之外，它还引入了一些OC语言不涵盖的功能的范例。Foundation预期实现以下几个功能：

* 提供少量的基本的实用工具类；
* 为了降低开发的难度，引入了一些统一的约定，比如dealloc函数；
* 支持Unicode字符串、对象持久性和对象分布；
* 提供了一个标准的操作系统独立,提高可移植性。

<!--more -->

### 2. ViewController 中回调
* 使用delegate （weak delegate, 在外面传进来  xx.delegate = self or something, 先要定义protocol)
* 使用block (外面传block, 先要定义block type)
* 使用property
* 使用带参数消息

### 3. 增加子ViewControlller
ViewController是管理View的，View就是一个UI。
增加子ViewController的正确方法是先增加子ViewController，再增加子View，目的是保证ViewController的history.Modal不用此方法。
``` objective-c
  [self.leftSidebarContainerViewController addChildViewController:self.leftSidebarViewController];
  [self.leftSidebarContainerViewController.view addSubview:self.leftSidebarViewController.view];
```

### 4. modal
modal 使用presentViewController呈现，（需要一个容器ViewController?可能是）。
那么取消呢？
使用modal view 自身的dismissViewControllerAnimated或者它容器的dismissViewControllerAnimated。效果是一样的,最终都是容器去执行。

> Modal View Controller
When a View Controller is presented modally, you can dismiss it (from the second view controller) by calling

``` objective-c
[self dismissViewControllerAnimated:YES completion:nil];
```

> The presenting view controller is responsible for dismissing the view controller it presented. If you call this method on the presented view controller itself, UIKit asks the presenting view controller to handle the dismissal.

So it works for the presented view controller to call it on itself.

[stack overflow](https://stackoverflow.com/questions/14636891/dismissing-a-presented-view-controller/14637067#14637067)
