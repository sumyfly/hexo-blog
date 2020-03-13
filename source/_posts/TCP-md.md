---
title: TCP.md
date: 2020-03-13 11:30:48
tags:

categories: Networking
---
### 1. 名称解释
可以用WireShark通过分析Tcp报来得知，工具显示得很清楚。

#### 1.1 10个控制标志位 Flag
下面的含义只针对其标志位为1的情况，比如SYN=1才代表握手阶段。FIN=1代表挥手阶段。

- SYN 表示新建连接
- ACK 表示响应
如果发送数据为空的话，可以发送同一个Seq的多个ACK，这个可以用来保活和窗口协商。这个单个ACK只能TCP协议实现发送，应用层只能发[ACK+PSH]
- FIN (Finish)表示关闭连接
- PSH (Push)表示传输数据
- RST (Reset)表示连接重置
这个RST的原因是因为close连接时，本地接受缓冲区还有数据。避免RST的方法就是调用close的之前，调用shutdown。netty称之为优雅关闭。

- Nonce
- CWR
- ECN-Echo
- Urgent

> Flags 一共16位，目前只用到9位，剩下的7位是保留位。

#### 1.2 Tcp传输数据中的字段
Seq(Sequence number): 本次发送得顺序号
Ack(Acknowledgment number): 确认号，其值 = `对方的seq` + `对方的Tcp Segement size`, 其含义代表我收到你seq号的数据了，长度是多少。
Next sequence number: 这个是期待对方返回的Ack值，这个不是在TCP协议内。

> 注意ACK是标志位, Ack是确认号。大小写不一样，含义也不同。

<!-- more -->