---
title: electron
date: 2018-04-10 11:27:55
tags:
---

### 1. Electron vs NW.js
#### 1.1 共同点
- 都是使用chromium作为渲染工具，开发跨平台的客户端软件。
- 完成的软件结构都是有两部分，native和web。前者是调用本地文件接口，网络等，实现底层功能，如数据库连接；后者和普通web开发一样。
- 编写语言都是js, html, css

#### 1.2 不同点
- Electron的启动文件是一个js文件,在里面load一个index.html；NW.js是一个index.html。这是设计上的区别，Electron实现是不改chromium+node；NW.js修改chromium，以支持node的接口。
- Electron的代码只可以混淆；NW.js可以改原生chromium的代码，安全性比Electron高。

<!-- more -->

#### 1.3 Electron的用户
- VS Code
- Slack
- Cocos Creator

#### 1.4 nw.js的用户
- 微信开发工具，小程序开发工具

感觉前端统一化趋势越来越明显，很快就会有更加通用的方案： 更加动态化，更加统一。其实那就是说明JavaScript的应用场景会更多。我看好React的一点就是因为js的编程能力比增强html的语义实在是强太多了。而且JSX的可读性、结构化表现能力也不弱于html。*所以Google的Flutter方案我并不看好，渲染的结构能力比起JSX太差，单纯有性能优势而没有开发优势不会太长久，但还是有待观察它进一步表现, 可能就是一个坑，想想IOS怎么也不会支持Google的方案，最多支持RN。*