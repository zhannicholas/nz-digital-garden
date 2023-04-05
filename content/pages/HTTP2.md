---
tags:
- HTTP
title: HTTP2
categories:
date: 2022-06-19
lastMod: 2023-04-04
---


## RFC

  + [RFC7540: Hypertext Transfer Protocol Version 2 (HTTP/2)](https://httpwg.org/specs/rfc7540.html#).

  + [RFC8740: Using TLS1.3 with HTTP/2](https://httpwg.org/specs/rfc8740.html).

  + [RFC9113: HTTP/2](https://httpwg.org/specs/rfc9113.html).

In particular, HTTP/1.0 allowed only one request to be outstanding at a time on a given TCP connection. HTTP/1.1 added request pipelining, but this only partially addressed request concurrency and still suffers from head-of-line blocking. Therefore, HTTP/1.0 and HTTP/1.1 clients that need to make many requests use multiple connections to a server in order to achieve concurrency and thereby reduce latency.

The basic protocol unit in HTTP/2 is a frame. The client is the TCP connection initiator.

HTTP/2 最主要的特性是**多路复用**（multiplexing）。多路复用允许在一个 TCP 连接上发送多个数据流（stream)，减少了连接的使用。

## Frame


  + Frame is the smallest unit of communication within an HTTP/2 connection, consisting of a header and a variable-length sequence of octets structured according to the frame type.

  + The structure and content of the frame payload is dependent entirely on the frame type.

  + ### Frame Layout


    + All frames begin with a fixed 9-octet header followed by a variable-length frame payload.

`
HTTP Frame {
  Length (24),
  Type (8),

  Flags (8),

  Reserved (1),
  Stream Identifier (31),

  Frame Payload (..),
}
`

`
+-----------------------------------------------+
 |                 Length (24)                   |
 +---------------+---------------+---------------+
 |   Type (8)    |   Flags (8)   |
 +-+-------------+---------------+-------------------------------+
 |R|                 Stream Identifier (31)                      |
 +=+=============================================================+
 |                   Frame Payload (0...)                      ...
 +---------------------------------------------------------------+
`

    + The structure and content of the frame payload are dependent entirely on the frame type.

## Streams


  + A "stream" is an independent, bidirectional sequence of frames exchanged between the client and server within an HTTP/2 connection.

  + ### Stream State

    + [Stream State Figure](https://httpwg.org/specs/rfc9113.html#StreamStatesFigure).

  + ### Error Handling


    + HTTP/2 framing permits two classes of errors:

      + An error condition that renders the entire connection unusable is a connection error. A connection error is any error that prevents further processing of the frame layer or corrupts any connection state.

      + An error in an individual stream is a stream error. A stream error is an error related to a specific stream that does not affect processing of other streams.

## HTTP/2 Connections


  + ### Connection Management

    + HTTP/2 connections are persistent.

  + 

## TCP 队头阻塞

  + TCP 是一个基于连接的可靠传输协议，连接的两端之间有一个虚拟链路，其中一端发送的内容要么在正常的情况下以相同的顺序出现在另一端，要么因为连接中断而丢失。

  + 如果一个 HTTP/2 连接中出现丢包，或者连接中断，整个 TCP 连接都会被暂停，丢失的数据包需要重新传输，而这个丢失的数据包之后的所有数据包都需要等待。这种单个数据包造成的阻塞，就是 TCP 队头阻塞。

  + 


