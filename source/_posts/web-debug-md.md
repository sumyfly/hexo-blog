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

### 4.Promise
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

### 5. dot
Use `&middot;` · for a dot or `&bull;` • for a thicker, bulleted list style dot.

### 6. post
post 的body要进行JSON.stringify处理，   

``` js
  transformRequestBody: data => {
    return typeof data == 'object' ? JSON.stringify(data) : data
  }, // change RequestBody before fetch
```

### 7. Object.assign 参数有个数组
Object.assign({}, res),如果res是个array,那么结果就是一个object，所以在Object.assign前，要确定是否需要一个array.

### 8. div的文本没有自动换行
默认情况下，一个 DIV或者其他元素的文本，如果都是无文字分隔符，无空格，则不会自动换行，比如:
``` html
<div class='div-test'>012345555555555555555555555555555555555555555555555555555555</div>

<!--加上下面的样式即可解决-->
.div-test {
  word-break: break-all;
}
```

### 9. 文本空白符处理
使用`whiteSpace: 'pre-wrap'`,但此时有个问题，`textIndent`无效。