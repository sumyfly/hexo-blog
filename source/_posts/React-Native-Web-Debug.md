---
title: React-Native-Web-Debug
date: 2017-12-19 20:15:00
tags:
 - react-native-web
---

### 1.Image
react-native-web中把Image转为了div,使用了bacground-image:url('xxxx'),还有background-size:'contain' or 'cover'

### 2.mobile safari remote debug
这个时候是无痕模式，没有WebSQL写入权限，可以用cookie。

### 3. zIndex导致的穿透
错误设置了zIndex，导致一个Tag显示在Modal层之上。一开始我怀疑是Modal层的问题，比如overflow或者position,backgroundColor，验证不是之后。因为Dom树，React的渲染树没有问题。后来才发现Tag多了一个zIndex:5，不知道谁加的，显然这个是不必要的。所以去除后，bug修复。zIndex可以穿透position: fixed的Modal层，所以和mobile app不一样，mobile app上Modal层是独立原生的，靠zIndex穿透不了。

### 4. 微信浏览器缓存
nginx的缓存设置成{expires -1},意思是永不缓存。
