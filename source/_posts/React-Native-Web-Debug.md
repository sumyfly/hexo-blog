---
title: React-Native-Web-Debug
date: 2017-12-19 20:15:00
tags:
 - react-native-web
categories: React-Native
---

### 1.Image
react-native-web中把Image转为了div,使用了bacground-image:url('xxxx'),还有background-size:'contain' or 'cover'

### 2.mobile safari remote debug
这个时候是无痕模式，没有WebSQL写入权限，可以用cookie。

### 3. zIndex导致的穿透
错误设置了zIndex，导致一个Tag显示在Modal层之上。一开始我怀疑是Modal层的问题，比如overflow或者position,backgroundColor，验证不是之后。因为Dom树，React的渲染树没有问题。后来才发现Tag多了一个zIndex:5，不知道谁加的，显然这个是不必要的。所以去除后，bug修复。zIndex可以穿透position: fixed的Modal层，所以和mobile app不一样，mobile app上Modal层是独立原生的，靠zIndex穿透不了。

### 4. 微信浏览器缓存
nginx的缓存设置成{expires -1},意思是永不缓存。

### 5. div下的View
比如我用的`react-moda`,在其children中设置`flex:1`,chrome没有问题，ios 11也么有问题，ios就有问题。原因是设置`flex:1`的元素其父容器没有设置width和height。解决方案就是不用flex,用固定width和height。但是我不明白的就是
``` js
//View 没有任何style。children都设置了width和height
<View>{children}</View> 
//**********
{children} //children都设置了width和height
//有什么区别?
```
有的时候，没有显示内容，解决方案有下面:

  - 设置固定width,height
  - 在`<View>`上，加上一个div，设置它的style，display:flex