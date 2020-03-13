---
title: websocket
date: 2020-02-18 00:02:03
tags:

categories: Networking
---

### 1. WebSocket介绍
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

WebSocket不需要使用http header了，TODO: 但是如何标定数据长度呢?

### 2. WebSocket如何保持长链接
#### 2.1 心跳机制
Ping/Pong Frame (RFC6455 - The WebSocket Protocol)。这种 Frame 是一种特殊的数据包，它只包含一些元数据而不需要真正的 Data Payload，可以在不影响 Application 的情况下维持住中间网络的连接状态。

#### 2.2 重连机制
TODO: 实验部分，先链接，然后物理断开链接，抓包。


### 3. 握手
>> localhost

GET / HTTP/1.1
Connection: Upgrade
Host: localhost:3000
Sec-WebSocket-Key: wVCXyrMuAjIS21Rknz8NCA==
Sec-WebSocket-Version: 13
Upgrade: websocket



### 4. 消息类型
当客户端和服务端都发送了他们的握手协议，并且当握手已经成功，那么数据传输就开始了。这是一个双方都可以独立发送任意数据的双向通信渠道。
在握手成功以后，客户端和服务端传输的数据来回传输的数据单位，我们在规范中称为消息（messages）。在传输中，一条消息有一个或者多个帧组成。WebSocket中的消息不需要对应特定网络层中的帧，一条零散的消息可能由中间人合并或者拆分成网络层的帧。
帧有关联的类型。同一条消息的每一帧都包含相同类型的数据。通常来说，它可以是文本数据（UTF-8编码）、二进制数据（留给应用解析的数据）和控制帧数据（不是用来传输数据，而是用来作为协议层的特定符号，如关闭连接帧）。当前版本的协议定义了6中控制帧类型并且预留了10个保留类型。





