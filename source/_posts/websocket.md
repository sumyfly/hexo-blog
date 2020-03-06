---
title: websocket
date: 2020-02-18 00:02:03
tags:
---


WebSocket技术主要是为了解决Ajax和Comet里XMLHttpRequest附带的缺陷所引起的问题。
一旦Web服务器与客户端之间建立起WebSocket协议的通信连接，之后所欲的通信都依靠这个专用协议进行。通信过程中可互相发送JSON、XML、HTML或图片等任意格式的数据。
由于是建立在HTTP基础上的协议，因此连接的发起方仍然是客户端，但是连接一旦建立通信后，无论是客户端还是服务器端，都可以直接向对方发送报文。

WebSocket协议的主要特点：
- 服务器端推送功能
- 减少通信量

<!-- more -->

为实现WebSocket通信，在HTTP连接建立之后，需要完成一次“握手”步骤，需要用到HTTP的Upgrade首部字段，告知服务器通信协议发生改变，以达到握手的目的。（握手响应状态码：101）
成功握手确立WebSocket连接之后，通信时不再使用HTTP的数据帧，而采用WebSocket独立的数据帧。
WebSocket的URL格式
ws://example.com/
WebSocket协议支持全双工通信，因此服务器端不必等待请求，可直接发送数据。
JavaScript可调用“The WebSocket API”内提供的WebSocket程序接口，以实现WebSocket协议下的全双工通信。

``` js
调用WebSocket API，每50ms发送一次数据
var socket = new WebSocket ('ws://game.example.com:12010/updates')
socket.onopen = function () {
  setInterval(function() {
    if(socket.bufferedAmout == 0)
      socket.send(getUpdateData())
  },50)
}
```