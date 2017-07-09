---
title: React-Native-Debug
date: 2017-07-08 22:28:36
tags: 
  - react-native 
  - debug
categories: React-Native
---

### 1. Image overflow
Image是可以overflow:display,这个在android和ios都有效果。而View在android上不可以overflow:display的。

<!-- more -->
### 2. flex layout

父节点flex:1
1)子节点如果没有设置flex布局，那么宽度为其父节点宽度。
2)子节点如果设置了flex布局，那么宽度为其内容宽度。

### 3. postion:absolute
position:absolute不在flow中，忽视flex:1布局。其位置,大小由left,right,top,bottom设置，大小也可以由内容大小确定。
