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
TODO: 实验部分，先链接，然后物理断开链接，抓包。Socket.IO有短线重连机制，Java-WebSocket还没有发现有这个机制，但是两者都有心跳。
TODO: 是不是一个Server对应一个Client只能有一个WebSocket链接？

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



### 5. Socket.IO
心跳包： 25秒
Server -> Client: 0x81 0x01 0x33
Client -> Server: 不固定

强制关闭Server: Client收到RST，尝试重连频率5秒一次。当Server重新启动后，Client会自动重连。然后继续发送心跳包。

Socket.IO和WebSocket不是同一种东西，Socket.IO参照了WebSocket，并且有降级方案，但是本质是它们不一样，握手协议不一样，所以Socket.IO的server不能用标准客户端连接，反之亦然。


> Socket.IO是js的库，用于web的开发应用中实现客户端与服务端建立全双工通信。SocketIO主要是基于websocket协议进行的上层封装（包括连接的管理、心跳与维活及提供room的广播机制与异步io等特性），同时在websocket不可用时，提供长轮询作为备选方式获取数据。

这里要注意就是Socket.IO不是Websocket的实现，Socker.IO有自己的协议说明，因此和websocket的server不兼容，Socker.IO握手及数据传输都有自定义的metadata与认证逻辑，比如头部的sid，作者在刚使用时上层接了负载均衡，没有考虑session保持，导致Socket.IO握手时鉴权一直不通过。

### 6. WebSocket 协议控制
WebSocket称数据为"message"。

在WebSocket协议中，数据是通过一系列数据帧来进行传输的。为了避免由于网络中介（例如一些拦截代理）或者一些在第10.3节讨论的安全原因，客户端必须在它发送到服务器的所有帧中添加掩码（Mask）（具体细节见5.3节）。（注意：无论WebSocket协议是否使用了TLS，帧都需要添加掩码）。服务端收到没有添加掩码的数据帧以后，必须立即关闭连接。在这种情况下，服务端可以发送一个在7.4.1节定义的状态码为1002（协议错误）的关闭帧。服务端禁止在发送数据帧给客户端时添加掩码。客户端如果收到了一个添加了掩码的帧，必须立即关闭连接。在这种情况下，它可以使用第7.4.1节定义的1002（协议错误）状态码。（这些规则可能会在将来的规范中放开）。
基础的数据帧协议使用操作码、有效负载长度和在“有效负载数据”中定义的放置“扩展数据”与“引用数据”的指定位置来定义帧类型。特定的bit位和操作码为将来的协议扩展做了保留。
一个数据帧可以在开始握手完成之后和终端发送了一个关闭帧之前的任意一个时间通过客户端或者服务端进行传输（第5.5.1节）。

``` 
      0                   1                   2                   3
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
     +-+-+-+-+-------+-+-------------+-------------------------------+
     |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
     |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
     |N|V|V|V|       |S|             |   (if payload len==126/127)   |
     | |1|2|3|       |K|             |                               |
     +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
     |     Extended payload length continued, if payload len == 127  |
     + - - - - - - - - - - - - - - - +-------------------------------+
     |                               |Masking-key, if MASK set to 1  |
     +-------------------------------+-------------------------------+
     | Masking-key (continued)       |          Payload Data         |
     +-------------------------------- - - - - - - - - - - - - - - - +
     :                     Payload Data continued ...                :
     + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
     |                     Payload Data continued ...                |
     +---------------------------------------------------------------+
```

#### 6.1 Control Flags
1... .... FIN 表示是消息的最后一个片段，不同于TCP的FIN
.000 .... Reserved
.... 0001 Opcode 
0... .... Mask 服务端必须不带Mask，客户端必须带Mask，不符合这规则，应该主动断开链接。

- Opcode
​ %x0 表示一个持续帧
​ %x1 表示一个文本帧
​ %x2 表示一个二进制帧
​ %x3-7 预留给以后的非控制帧
​ %x8 表示一个连接关闭包
​ %x9 表示一个ping包
​ %xA 表示一个pong包
​ %xB-F 预留给以后的控制帧

所以加上FIN的半个字节，一开始的字节，0x89代表PING， 0x8A代表PONG

#### 6.2 Payload

https://www.jianshu.com/p/2ec3d20341ab

### 7. WebSocket JAVA

心跳包: 
Server -> Client: 0x8a 0x00 || 0x89 00
Client -> Server: 不固定