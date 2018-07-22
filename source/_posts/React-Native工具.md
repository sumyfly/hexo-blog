---
title: React-Native工具
date: 2018-07-22 23:02:18
tags:
 - react-native tools
categories: React-Native
---

### 1. 调试工具
#### 1.1 Facebook官方react-devtools
特点是命令行启动，不需要RN启动remote js debugging.只能看React布局，如同Chrome的React插件，不能看网络，不能看Redux。
#### 1.2 Electron集成调试工具 React-Native-Debugger
特点是：独立的Electron程序，集成了react-devtools, Redux, chrome的调试。需要启动remote js debugging,而且要关掉默认的chrome的调试网页。
集成Redux调试工具时，需要添加增强代码。
``` js
const composeFuncs = [applyMiddleware(
  sagaMiddleware,  
  asyncInitialState.middleware(loadStore),
)]

if (__DEV__) {
  // to integrate React Native Debugger
  composeFuncs.push(
    // __REDUX_DEVTOOLS_EXTENSION__ this is React Native Debugger added
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  )
}
```
> React的调试框中的$r，直接可以在Chrome的console中读到。
<!-- more -->
