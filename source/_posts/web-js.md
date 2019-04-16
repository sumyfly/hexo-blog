---
title: web-js
date: 2018-04-11 23:49:49
tags:
 - web
 - javascript
---
### 1.事件三个阶段
- capture，从DOM tree到节点target
- target, 处理事件
- bubble，事件冒泡，如果不stopPropagation或stopImmediatePropagation,那么一直到DOM
> preventDefaut()这里的Default会stopPropagation,不让冒泡。冒泡有时候很有用，如监听页面的点击，做分析报告。另一个好处是可以动态添加和移除节点，同时可以对它的事件监听。

<!-- more -->

### 2.DOMContentLoaded
这个事件和`onload`的区别是：DOMContentLoaded只是document解析好了，onload是document, css, js都下载解析好了。注意同步js会暂停document的解析，原因是js可能动态添加节点。
``` js
document.addEventListener("DOMContentLoaded", function(event) {
    console.log("DOM fully loaded and parsed");
  });
```
https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded#Related_Events

### 3. onclick
三种方法:
``` js
function handler(e){}
// 1. jquery
$('#foo').click(handler)

// 2. onclick
<button onclick="handler(this)"></button>

// 3. document.addEventListener
document.getElementById('foo').addEventListener('click', handler)
```
第二种方式只能增加一个handler,而第一种和第三种可以绑定多个handler,jquery还有缓存。addEventListener第三个参数`useCapture`是说事件是capture阶段还是bubble阶段。

### 4. preventDefault, stopPropagation
#### 4.1 preventDefault
取消标签默认动作，如`<input type="submit">`的submit，`<input type="checkbox">`的toggling事件,如果取消这个Default的运行的话，继续冒泡,直到事件被处理或者到DOM tree。

#### 4.2 stopPropagation
stopPropagation、stopimmediatepropagation都是取消冒泡，不向DOM tree冒泡了。

#### 4.3 React中的事件
React中的事件是虚拟事件，不同于DOM中的事件，所以不需要preventDefault和stopPropagation。React应该用的是全局监听，我的猜测？

### 4. template for web
模板引擎（Template Engine）, 是用来解析对应类型模板文件然后动态生成由数据和静态页面组成的视图文件的一个工具。 它通过标签(tag)来响应各种解析动作，通过变量占位的方式动态的将对应数据展示到指定位置。

> 不过前端工作，最多的是CSS和JS调试，模板就是生成html，所以模板最好以html结构为基础。

#### 4.1 ejs
ejs，它是一个开源的javaScript模板库或工具，ejs将数据和模板整合最终生成html文件。性能好，不支持模本继承。

#### 4.2 jade
express默认模本，使用缩进不是很方便。

#### 4.3 handlebars
Handlebars 是 JavaScript 一个语义模板库，通过对view和data的分离来快速构建Web模板。它采用"Logic-less template"（无逻辑模版）的思路，在加载时被预编译，而不是到了客户端执行到代码时再去编译， 这样可以保证模板加载和运行的速度。Handlebars兼容Mustache，你可以在Handlebars中导入Mustache模板。

#### 4.4 Mustache
Mustache 是一个轻逻辑的模板语言（ Logic-less templates），mustache 是一个js模板,用于展示和js分离，它的优势在于可以应用在 Javascript、PHP、Python、Perl 等多种编程语言中。原本是基于javascript 实现的模板引擎,类似于 freemark和valicity ，但是比freemark和valicity更加轻量级更加容易使用，经过拓展目前支持javascript，java，.NET，PHP，C++等多种 平台下开发! 

https://github.com/baryshev/template-benchmark
https://blog.csdn.net/wulove52/article/details/85860192
https://www.cnblogs.com/JoannaQ/p/3462318.html