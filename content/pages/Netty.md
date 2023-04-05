---
tags:
- Netty
title: Netty
categories:
date: 2023-01-14
lastMod: 2023-01-14
---


Boss Group vs Worker Group

  + Boss Group 负责处理连接，Worker Group 负责处理实际的 IO 读写。

Channel, ChannelEvent, ChannelHandler, ChannelPipeline

  + Upstream vs Downstream

    + Upstream 表示 inbound，即读操作。

    + Downstream 表示 outbound，即写操作

  + ChannelPipeline

    + [Building a pipeline](https://netty.io/4.0/api/io/netty/channel/ChannelPipeline.html)

      + A user is supposed to have one or more [`ChannelHandler`](https://netty.io/4.0/api/io/netty/channel/ChannelHandler.html)s in a pipeline to receive I/O events (e.g. read) and to request I/O operations (e.g. write and close). For example, a typical server will have the following handlers in each channel's pipeline, but your mileage may vary depending on the complexity and characteristics of the protocol and business logic:

        + Protocol Decoder - translates binary data (e.g. [`ByteBuf`](https://netty.io/4.0/api/io/netty/buffer/ByteBuf.html)) into a Java object.

        + Protocol Encoder - translates a Java object into binary data.

        + Business Logic Handler - performs the actual business logic (e.g. database access). and it could be represented as shown in the following example:

`
static final [EventExecutorGroup](https://netty.io/4.0/api/io/netty/util/concurrent/EventExecutorGroup.html) group = new [DefaultEventExecutorGroup](https://netty.io/4.0/api/io/netty/util/concurrent/DefaultEventExecutorGroup.html)(16);
 ...

 [ChannelPipeline](https://netty.io/4.0/api/io/netty/channel/ChannelPipeline.html) pipeline = ch.pipeline();

 pipeline.addLast("decoder", new MyProtocolDecoder());
 pipeline.addLast("encoder", new MyProtocolEncoder());

 // Tell the pipeline to run MyBusinessLogicHandler's event handler methods
 // in a different thread than an I/O thread so that the I/O thread is not blocked by
 // a time-consuming task.
 // If your business logic is fully asynchronous or finished very quickly, you don't
 // need to specify a group.
 pipeline.addLast(group, "handler", new MyBusinessLogicHandler());
`

    + 

`txt
                                       I/O Request
                                     via {@link Channel} or
                                 {@link ChannelHandlerContext}
                                           |
  +----------------------------------------+---------------+
  |                  ChannelPipeline       |               |
  |                                       \|/              |
  |  +----------------------+  +-----------+------------+  |
  |  | Upstream Handler  N  |  | Downstream Handler  1  |  |
  |  +----------+-----------+  +-----------+------------+  |
  |            /|\                         |               |
  |             |                         \|/              |
  |  +----------+-----------+  +-----------+------------+  |
  |  | Upstream Handler N-1 |  | Downstream Handler  2  |  |
  |  +----------+-----------+  +-----------+------------+  |
  |            /|\                         .               |
  |             .                          .               |
  |     [ sendUpstream() ]        [ sendDownstream() ]     |
  |     [ + INBOUND data ]        [ + OUTBOUND data  ]     |
  |             .                          .               |
  |             .                         \|/              |
  |  +----------+-----------+  +-----------+------------+  |
  |  | Upstream Handler  2  |  | Downstream Handler M-1 |  |
  |  +----------+-----------+  +-----------+------------+  |
  |            /|\                         |               |
  |             |                         \|/              |
  |  +----------+-----------+  +-----------+------------+  |
  |  | Upstream Handler  1  |  | Downstream Handler  M  |  |
  |  +----------+-----------+  +-----------+------------+  |
  |            /|\                         |               |
  +-------------+--------------------------+---------------+
                |                         \|/
  +-------------+--------------------------+---------------+
  |             |                          |               |
  |     [ Socket.read() ]          [ Socket.write() ]      |
  |                                                        |
  |  Netty Internal I/O Threads (Transport Implementation) |
  +--------------------------------------------------------+
`

Client 使用 `connect()`，Server 使用 `bind()`。
