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
宽高度应该是确定的，或者是可以推断的，如子节点宽高度，父容器宽高度。如果父节点、子节点皆不能推断，那么显示就会不确定，有问题。

父节点->子节点
1)子节点如果没有设置flex布局（alignItems,justifyContent），那么宽度为其父节点宽度,高度由内容决定。
2)子节点如果设置了flex布局，那么宽高度为其内容宽高度。
3)子节点设置了宽高度，那么以此宽高度为准。

父节点flex:1
1)如果父节点flex:1不能推断大小，那么子节点flex:1也没有效果

### 3. postion:absolute
position:absolute不在flow中，忽视flex:1布局。其位置,大小由left,right,top,bottom设置，大小也可以由内容大小确定。

### 4. Text
如果元素为Text，其内容过长，并不会换行，而是一直显示下去。如果设置了宽度，或者推断出宽度，那么就会换行。

### 5. borderRadius
有时候borderRadius设置了，但是只有下半部份有效果，那么设置一下overflow:hidden

### 6. setState
setState是异步的，在contructor里面初始化this.state={...},然后紧接着设置setState,那么可能初始化值覆盖了后面的setState，所以setState这里并不可靠。放到didMount里面去设置。

### 7. ListView 中的onEndReached
不能设置contentContainerStyle,不然onEndReached不能再安卓上正确触发(初始化后的第一个onEndReached不能fired)。


