---
title: iOS
date: 2020-03-06 14:46:32
tags:
---

### 1. 布局方式
iOS有三种基本的界面布局的方法，分别是手写UI，xib和storyboard。手写UI是最早进行UI界面布局的方法，优点是灵活自由，缺点是需要写大段的代码进行布局。xib也是比较早出现的UI布局的方式，优点是不需要手写代码，但是每个界面对应一个xib，管理起来复杂。而storyboard则是在iOS5以后出现的，是苹果官方主推的一个代替xib的策略，不仅能将xib汇总统一管理，还可以描述各种场景之间的过渡，缺点是多人协作开发时容易产生冲突。

链接：https://www.jianshu.com/p/279162f77354

<!-- more -->

#### 1.1 Constraints，Layout，Draw调用顺序
一个页面更新的顺序一般为
调用约束计算出frame（Constraints）→ 根据计算出的frame重新布局(Layout) → 根据重新布局的结果进行图像渲染（Draw）

layout的改变会导致重新计算Constraints
layoutIfNeeded会调用updateConstraintsIfNeeded

Constraints的计算顺序是低到上 （从subview到superview）
layout的更新顺序是从顶到下（从superview到subview）

### 2.更新机制
一个 iOS 应用的主 Run loop 负责处理所有的用户输入事件并触发相应的响应。所有的用户交互都会被加入到一个事件队列中。下图中的 Application object 会从队列中取出事件并将它们分发到应用中的其他对象上。本质上它会解释这些来自用户的输入事件，然后调用在应用中的 Core objects 相应的处理代码，而这些代码再调用开发者写的代码。当这些方法调用返回后，控制流回到主 Run loop 上，然后开始 update cycle（更新周期）。Update cycle 负责布局并且重新渲染 view 们。

#### 2.1 Update Cycle
Update cycle 是当应用完成了你的所有事件处理代码后控制流回到主 Run loop 时的那个时间点。正是在这个时间点上系统开始更新布局、显示和设置约束。如果你在处理事件的代码中请求修改了一个 view，那么系统就会把这个 view 标记为需要重画（redraw）。在接下来的 Update cycle 中，系统就会执行这些 view 上的更改。用户交互和布局更新间的延迟几乎不会被用户察觉到。iOS 应用一般以 60 fps 的速度展示动画，就是说每个更新周期只需要 1/60 秒。这个更新的过程很快，所以用户在和应用交互时感觉不到 UI 中的更新延迟。但是由于在处理事件和对应 view 重画间存在着一个间隔，run loop 中的某时刻的 view 更新可能不是你想要的那样。如果你的代码中的某些计算依赖于当下的 view 内容或者是布局，那么就有在过时 view 信息上操作的风险。理解 run loop、update cycle 和 UIView 中具体的方法可以帮助避免或者可以调试这类问题。下面的图展示出了 update cycle 发生在 run loop 的尾部。

#### 2.2 自动刷新触发器
有许多事件会自动给视图打上 “update layout”标记，因此 layoutSubviews 会在下一个周期中被调用，而不需要开发者手动操作。这些自动通知系统 view 的布局发生变化的方式有：

- 修改 view 的大小
- 新增子 view
- 用户在 UIScrollView 上滚动（layoutSubviews 会在 UIScrollView 和它的父 view 上被调用）
- 用户旋转设备
- 更新视图的 constraints

这些方式都会告知系统 view 的位置需要被重新计算，继而会自动转化为一个最终的 layoutSubviews 调用。当然，也有直接触发 layoutSubviews 的方法。

http://www.cocoachina.com/articles/22418

> Android和iOS都是60FPS的周期调用draw, 所以页面的更改其实不是实时的，而是以一个频率更新。