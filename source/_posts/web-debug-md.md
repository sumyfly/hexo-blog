---
title: web-debug.md
date: 2017-08-14 18:53:44
tags:
  - web
---

###1. 字体居中
我设置了flex, justifyContent: 'center',还是没用，后来发现是字体格式的问题，我用的HelveticaNeueLTPro-Cn.otf,这个字体的字下面有空白，所以居中不对，后来用了PingFangSC-Regular就好了，我就不知道为什么下面有空白？
https://stackoverflow.com/questions/27431681/why-is-my-font-face-adding-space-at-the-baseline

###2. mobile字体 mobile web字体
mobile字体不一定能用于mobile web字体，不知道为什么。因为我用的一个字体可以用于react-native,但是不能用于mobile web，格式是ttf。