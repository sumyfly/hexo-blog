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
  // integrate React Native Debugger
  typeof window === 'object' && window.__REDUX_DEVTOOLS_EXTENSION__ && composeFuncs.push(
    // __REDUX_DEVTOOLS_EXTENSION__ this is React Native Debugger added
    window.__REDUX_DEVTOOLS_EXTENSION__()
  )
}
```
- React的调试框中的$r，直接可以在Chrome的console中读到。
- CMD+D显示调试窗口后，启用Toggle Inspector可以在React中显示当前的View的信息。
<!-- more -->

### 2. Redux的调试工具
redux-devtools和redux-devtools-extension是2个不同的框架。 

* redux-devtools是侵入式的，是开发环境下开发redux一个强大的开发工具。它使用monitor实时监听您的store。需要安装模块。 
* 如果你不想安装任何模块侵入你的项目里。Redux DevTools Extension是个很好的选择，它支持chrome，Firefox以及360浏览器,它提供了大部分常用的监听器去配置你的项目，不需要安装任何模块。

#### 2.1 redux-devtools
DevTools for Redux with hot reloading, action replay, and customizable UI.To use Redux DevTools, you need to choose a “monitor”—a React component that will serve as a UI for the DevTools. Different tasks and workflows require different UIs, so Redux DevTools is built to be flexible in this regard. We recommend using `LogMonitor` for inspecting the state and time travel, and wrap it in a `DockMonitor` to quickly move it across the screen. 
https://github.com/reduxjs/redux-devtools

#### 2.2 redux-devtools-extension
这个分浏览器，有chrome, firefox, electron, others(use remote-redux-devtools).
https://github.com/zalmoxisus/redux-devtools-extension


redux-devtools
http://react-china.org/t/redux-redux-devtools/11071
https://www.cnblogs.com/zhuzhenwei918/p/7249357.html

redux-devtools-extension
https://blog.csdn.net/achenyuan/article/details/80884895
