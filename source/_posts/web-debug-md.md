---
title: web-debug.md
date: 2017-08-14 18:53:44
tags:
  - web
---

### 1.字体居中
我设置了flex, justifyContent: 'center',还是没用，后来发现是字体格式的问题，我用的HelveticaNeueLTPro-Cn.otf,这个字体的字下面有空白，所以居中不对，后来用了PingFangSC-Regular就好了，我就不知道为什么下面有空白？
https://stackoverflow.com/questions/27431681/why-is-my-font-face-adding-space-at-the-baseline

### 2.mobile字体 mobile web字体
mobile字体不一定能用于mobile web字体，不知道为什么。因为我用的一个字体可以用于react-native,但是不能用于mobile web，格式是ttf。

### 3.远程手机浏览器调试
1.adb forward tcp:9224 localabstract:chrome_devtools_remote
然后localhost:9224

2.chrome://inspect

<!-- more -->

### 4.微信公众号支付
JSSDK和下单的appId要一样，chooseWXPay的签名，后台签名用timeStamp，前端用timestamp.url签名，不含#
如果微信支付提示{"errMsg":"chooseWXPay:fail"}，一种可能是签名错误，一种可能是授权目录错误。
http://tieba.baidu.com/p/3961646394
https://example.com/weixin/pay#/home/xxx，支付授权目录要配置到home那一级，或者带个在#前加?来阻隔这个问题，这应该是微信的BUG。

> 我用的是hashHistory,所以要配置到/setting/:
https://app-test.xxx.com/auth/webapp/index.html#/setting/

> 有时候微信里打开链接，会自己加上参数?from=singlemessage&install=0,这个时候就可以用
https://app-tesst.xxx.com/auth/webapp

> ios微信浏览器路径识别到
https://app-test.xxx.com/auth/webapp/index.html#/

最终配置了三个路径
![微信授权目录配置](/images/wechat_pay.png)

### 5.Promise
``` js
async asyncFun(){
  return new Promise((resolve, reject)=>{
    if(a){
      resolve(n)
    }else{
      reject() //在安卓微信浏览器中，reject(), reject(undefined)都将导致await 等待，直至超时。这个应该是bug;reject(123)参数不为undeefined就可以。
    }
  })
}

try{
  const a = await asyncFun() // 如果是resolve, resolve的实参值赋给a,如果是reject,a为初始值
} catch(e){
  console.log(e) //e 就是reject的实参值
}
```

> async 函数返回值是一个Promise，如果需要得到resolve的值，需要async await或者then。当 async 函数返回一个值时，Promise 的 resolve 方法会负责传递这个值；当 async 函数抛出异常时，Promise 的 reject 方法也会传递这个异常值。如果 async 函数没有返回值，它会返回 Promise.resolve(undefined)。

### 6. dot
Use `&middot;` · for a dot or `&bull;` • for a thicker, bulleted list style dot.

### 7. post
post 的body要进行JSON.stringify处理，   

``` js
  transformRequestBody: data => {
    return typeof data == 'object' ? JSON.stringify(data) : data
  }, // change RequestBody before fetch
```

### 8. Object.assign 参数有个数组
Object.assign({}, res),如果res是个array,那么结果就是一个object，所以在Object.assign前，要确定是否需要一个array.

### 9. div的文本没有自动换行
默认情况下，一个 DIV或者其他元素的文本，如果都是无文字分隔符，无空格，则不会自动换行，比如:
``` html
<div class='div-test'>012345555555555555555555555555555555555555555555555555555555</div>

<!--加上下面的样式即可解决-->
.div-test {
  word-break: break-all;
}
```
http://www.alloyteam.com/2016/05/css-word-for-word-breaker-do-you-really-understand/

### 10. 文本空白符处理
使用`whiteSpace: 'pre-wrap'`,但此时有个问题，`textIndent`无效。

### 11. 置换元素
> CSS里，可替换元素（replaced element）的展现不是由CSS来控制的。这些元素是一类外观渲染独立于CSS的 外部对象。典型的可替换元素有`<img>`、 `<object>`、 `<video>` 和 表单元素，如`<textarea>`、 `<input>`。某些元素只在一些特殊情况下表现为可替换元素，例如 `<audio>` 和 `<canvas>` 。 通过 CSS content 属性来插入的对象 被称作 匿名可替换元素（anonymous replaced elements）。

**canvas不是块级元素。**

浏览器根据块级元素的标签和属性，来决定元素的具体显示内容。CSS在某些情况下会对可替换元素做特殊处理，比如计算外边距和一些auto值。
例如：浏览器根据`<img>`标签的src属性显示图片。根据标签的type属性决定显示输入框还是按钮。置换元素在其显示中生成了框，这也就是有的内联元素能够设置宽高的原因。这些置换元素往往没有实际内容，即是一个空元素。

> 非置换元素,浏览器中的大多数元素都是不可置换元素，即其内容直接展示给浏览器。

### 12.块元素 内联元素
#### 12.1块元素
|标签名称|用途|
|-|:-:|
|address | 地址||
|blockquote | 块引用|
|center | 举中对齐块|
|dir | 目录列表|
|div | 常用块级容易，也是css layout的主要标签|
|dl | 定义列表|
|fieldset | form控制组|
|form | 交互表单|
|h1 | 大标题|
|h2 | 副标题|
|h3 | 3级标题|
|h4 | 4级标题|
|h5 | 5级标题|
|h6 | 6级标题|
|hr | 水平分隔线|
|isindex | input prompt|
|menu | 菜单列表|
|noframes | frames可选内容（对于不支持frame的浏览器显示此区块内容）|
|noscript | 可选脚本|内容（对于不支持script的浏览器显示此内容）|
|ol | 排序列表|
|p | 段落|
|pre | 格式化文本|
|table | 表格|
|ul | 非排序列表|

#### 12.2 内联元素
|标签名称|用途|
|-|:-:|
|a | 锚点|
|abbr | 缩写|
|acronym | 首字|
|b | 粗体（不推荐）|
|bdo | bidi override|
|big | 大字体|
|br | 换行|
|cite | 引用|
|code | 计算机代码（在引用源码的时候需要）|
|dfn | 定义字段|
|em | 强调|
|font | 字体设定（不推荐）|
|i | 斜体|
|img | 图片|
|input | 输入框|
|kbd | 定义键盘文本|
|label | 表格标签|
|q | 短引用|
|s | 中划线（不推荐）|
|samp | 定义范例计算机代码|
|select | 项目选择|
|small | 小字体文本|
|span | 常用内联容器，定义文本内区块|
|strike | 中划线|
|strong | 粗体强调|
|sub | 下标|
|sup | 上标|
|textarea | 多行文本输入框|
|tt | 电传文本|
|u | 下划线|
|var | 定义变量|
|textarea|多行的文本输入|
|canvas|画布|
|video|视频|
|audio|音频|

#### 12.3块级元素和内联元素之间的转换：
- 1.display
    块元素默认display:block;
    行内非替换元素(a,span)默认为display:inline;
    **行内替换元素(input)默认为display:inline-block;**

    a.display:none;不显示该元素，也不会保留该元素原先占有的文档流位置。
    b.display:block;转换为块级元素。
    c.display:inline;转换为行内元素。
    d.display:inline-block;转换为行内块级元素。

- 2.float
  当把行内元素设置完float:left/right后，该行内元素的display属性会被赋予block值，且拥有浮动特性。行内元素去除了之间的莫名空白。

- 3.position
  当为行内元素进行定位时，position:absolute与position:fixed.都会使得原先的行内元素变为块级元素。

https://www.jianshu.com/p/d69878549d92

### 13. encodeURI和encodeURIComponent

#### 13.1 相同点
都是编码URL，替换特殊字符为16进制数，如空格替换为%20,'/'替换为%2F。

#### 13.2 不同点
##### 13.2.1 编码范围不同
encodeURL方法**不会**对下列字符编码 ASCII字母、数字、~!@#$&*()=:/,;?+'
endoceURLComponent方法**不会**对下列字符编码 ASCII字母、数字、~!*()'
所以encodeURLComponent的编码范围更大，适合编码http url中的参数部分,因为:/都被编码了

https://www.cnblogs.com/season-huang/p/3439277.html
https://www.cnblogs.com/hushuai-ios/p/5500162.html

### 14.<a> 
 a本身会触发一个链接，a的onclick会触发一个链接，onclick事件执行后会触发a本身的链接，为了阻止a本身链接的触发，需要对onclick事件的函数返回false。
return true：返回正常的处理结果。
return false：返回错误的处理结果;终止处理;阻止提交表单;阻止执行默认的行为。
return：把控制权返回给页面。
``` html
若将html代码中的
 <a href="linkPage"><img src="images/banner1.gif" id="adBanner" alt="ad banner"></a>
改为：
 <a href="#"><img src="images/banner1.gif" id="adBanner" alt="ad banner"></a>
 ```
此时去掉js代码中的return false，也可以实现同样的效果。