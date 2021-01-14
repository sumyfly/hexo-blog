---
title: flutter
date: 2018-08-03 15:47:34
tags:
---

### 1.flutter介绍
优点:
- 跨平台
- 使用widgets，打包rendering引擎，不使用原生控件。实际上就是用canvas重绘。这是UI部分的跨平台的实现。
- 使用plantform channels，分平台操作服务。这是系统功能部分的跨平台实现。
- 不使用bridge,打包使用AOT，性能跟原生一致，没多少损失。
缺点:
- Dart难用，生态系统比js差，大坑。
- 没有React写界面直观，为什么不用标签化写界面？
- widgets太少，轮子也少，需要自己造。

先观察，我觉得一个开源的软件好，必须生态好，就是说能吸引足够多的开发者，而吸引开发者的原因就是它帮助开发者解决了什么问题。Dart对开发者不友好就是个不好的起点，期待它改变。
![flutter](/images/flutter.png)

但是Dart比JS的执行效率高，性能好是个优势，但是JS普通APP的性能需求也可以满足了。

<!-- more -->