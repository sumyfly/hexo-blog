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
- bubble，事件冒泡，如果不stopPropagation或stopImmediatePropgation,那么一直到DOM
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
stopPropagation、stopImedaitePropgation都是取消冒泡，不向DOM tree冒泡了。

#### 4.3 React中的事件
React中的事件是虚拟事件，不同于DOM中的事件，所以不需要preventDefault和stopPropagation。React应该用的是全局监听，我的猜测？
