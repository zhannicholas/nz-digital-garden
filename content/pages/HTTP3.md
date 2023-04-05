---
tags:
- HTTP
title: HTTP3
categories:
date: 2022-08-30
lastMod: 2022-09-02
---


# Worth To Read

  + [HTTP/3 explained](https://http3-explained.haxx.se/).

  + 

HTTP/3 基于 QUIC 协议。QUIC 放弃了 TCP，转而采用 UDP，克服了 TCP 队头阻塞问题。在使用 QUIC 时，通信双方会建立一个连接。但是，连接上的各个数据流是相互独立的，如果有数据流中出现丢包，只有出现丢包的数据流才需要等待，不会影响到没有出现丢包的数据流。

QUIC 协议没有明文版本，建立 QUIC 连接时必须使用 TLS1.3 建立加密连接。QUIC 只有在加密协议协商时会发送几个明文的初始握手报文。


