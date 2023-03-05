---
date: "2020-12-13T17:19:57+08:00"
title: "HTTP：代理"
authors: Nicholas Zhan
categories:
  - HTTP
tags:
  - HTTP
draft: false
toc: true
---
Web**代理（Proxy）** 服务器是网络中的中间实体，位于客户端与服务器之间，扮演的是“中间人”的角色，负责在各端点之间来回传送HTTP报文。

## Web的中间实体
Web代理服务器是代表客户端完成事务处理的中间人。如果没有Web代理，HTTP客户端就要直接与服务器进行对话。而有了Web代理之后，客户端就可以与代理进行对话，然后代理代表客户端与服务器进行交流。客户端仍然可以完成事务处理，但代理可以为它带来更加优质的服务和更加灵活的扩展功能。

HTTP代理服务器既是Web客户端，又是Web服务器。对于客户端而言，Web代理扮演的是服务器的角色，它必须正确处理请求并进行响应。同时，对于服务器而言，Web代理扮演的是客户端的角色，它向服务器发起请求并获得响应。

![A proxy must be both a server and a client](/images/computer_networks/http/a-proxy-must-be-both-a-server-and-a-client.png)

### 代理vs网关
严格来说，代理连接的是两个或多个使用相同协议的应用程序，而网关连接的是两个或多个使用不同协议的端点。网关的主要作用是进行协议转换，即使客户端与服务器使用了不同的协议，客户端也可以借助它完成与服务器之间的事务处理。

![Proxies vesus Gateways](/images/computer_networks/http/proxies-versus-gateways.png)

## 代理的使用场景
代理服务器可以监视并修改所有经过的HTTP流量，因此可以用它来实现很多有用的功能。例如：
1. 进行内容的过滤、访问控制、路由和转换。
2. 作为防火墙使用。
3. 作为Web缓存使用，维护常用内容的副本，以加快访问速度。
4. 假扮Web服务器，接收发给Web服务器的真实请求并处理。这种代理被称为**替代物（surrogate）**或**反向代理（reverse proxy）**。与普通Web服务器不同的是，反向代理可以发起与其它服务器的通信，按需获取请求内容。反向代理还可以用来提高访问慢速Web服务器上公共内容时的速度，通常将这些反向代理称为**服务器加速器（server accelerator）**。
5. 匿名代理。主动从HTTP报文中删除访问者的身份信息，从而提供高度的私密性和匿名性。

## 如何将请求导向代理
客户端通常会直接与Web服务器进行通信。为了让代理派上用场，我们需要将到客户端的HTTP流量导向代理，常见的方式有四种：
* 修改客户端（图a）。很多Web客户端都支持手工和自动的代理配置，可以将客户端配置为使用代理服务器。
* 修改网络（图b）。网络基础设施可以在客户端不知情的情况下通过一些技术手段将客户端的流量拦截并导入代理，这种代理通常被称为*拦截（intercepting）*代理。
* 修改DNS的命名空间（图c）。放在Web服务器之前的代理可以直接假扮Web服务器的名字和IP地址，这样，请求就可以直接被发给代理而不是服务器了。要实现这一点，我们可以手动修改DNS列表，也可以采用特殊的动态DNS服务器。
* 修改Web服务器（图d）。可以将某些Web服务器配置为向客户端发送一个HTTP重定向命令，将客户端的请求重定向到一个代理上去。

![Direct web requests to proxies](/images/computer_networks/http/direct-web-requests-to-proxies.png)

## 客户端代理设置
现在几乎所有的浏览器都允许用户对代理的使用进行配置。配置的方式大概有下面几种:
* 手工配置。显示地设置使用的代理。
* 预先配置浏览器。浏览器厂商预先对浏览器进行配置。
* 代理的自动配置（Proxy Auto-Configuration, PAC）。提供一个指向JavaScript编写的PAC文件的URI，客户端会去获取这个文件并运行，从而决定是否使用以及使用哪个代理服务器。PAC文件的后缀通常是`.pac`，MIME类型通常是`application/x-ns-proxy-autoconfig`。
* 使用*Web代理自动发现协议（Web Proxy Autodiscovery Protocol, WPAD）*。WPAD协议的算法会使用发现机制逐级上升策略自动地为浏览器发现合适的PAC文件。协议会使用一系列的资源发现技术来判定出适当的PAC文件。

## 追踪报文
Web请求在从客户端传送到服务器的这个过程中，经过两个或多个代理是非常常见的。随着代理的流行，我们要能够追踪经过代理的报文流，以检测出各种问题，其重要性就跟追踪经过不同交换机和路由器传输的IP数据报流一样。

### Via首部
`Via`首部字段列出了报文途径的每个中间节点（代理或网关）的相关信息，报文每经过一个节点，都必须将这个中间节点添加到`Via`列表的末尾。例如，下面的报文经过了两个代理：第一个代理为`proxy1.com`，它实现了HTTP/1.1协议；第二个代理为`proxy2.com`，它实现了HTTP/1.0：
```
Via: 1.1 proxy1.com, 1.0 proxy2.com
```
`Via`首部被用来追踪报文的转发过程、诊断报文路由循环，以及识别整个请求/响应链上所有发送方的协议处理能力。

#### Via的语法
`Via`首部包含一个由逗号分隔的*路径点（waypoint）*。每个路径点都对应一个独立的代理服务器或网关，并且含有与该中间节点的一些信息（比如所采用的协议、节点地址等）。`Via`首部的语法如下：
```
Via               = "Via" ":" 1#( waypoint )
waypoint          = ( received-protocol received-by [ comment ] )
received-protocol = [ protocol-name "/" ] protocol-version
received-by       = ( host [ ":" port ] ) | pseudonym
```

#### Via的请求与响应路径
请求和响应报文都会经过代理进行传输。因此，请求和响应报文中都要有`Via`首部。由于请求和响应通常是在同一条TCP连接上传送的，所以响应报文会在请求报文的路径上反向传输。因此，响应报文的`Via`首部几乎总是与请求报文的`Via`首部相反。

![The response Via is usually the reverse of the request Via](/images/computer_networks/http/the-response-via-is-usually-the-reverse-of-the-request-via.png)

### TRACE方法
通过HTTP/1.1提供的TRACE方法，用户可以追踪请求报文经过的代理链，观察请求报文经过了哪些代理以及每个代理都对请求报文做了哪些修改。

TRACE对代理流的调试非常有用，当TRACE请求到达服务器时，整条请求报文都会被封装在一条HTTP响应的body中会送给客户端。客户端通过检查响应报文，便可以知道报文所经过的代理列表。TRACE响应的Content-Type为`message/http`，状态码为`200 OK`。

通常，不管客户端与服务器之间存在多少代理，TRACE报文都会沿着这个代理链传到服务器。但我们可以使用`Max-Forwards`首部来限制TRACE和OPTIONS请求所经过的代理数。`Max-Forwards`请求首部包含了一个整数，这个整数代表当前请求报文还可以被转发多少次。当值为0时，不管接收者是谁，它都必须停止转发并将TRACE报文会送给客户端。当值大于0时，接收者会将其减一并转发。

## 代理的互操作性
客户端、服务器和代理通常是由不同厂商构建的，实现的HTTP规范版本可能会存在差异。通过HTTP提供的OPTIONS方法，客户端或代理可以发现Web服务器或其上某个特定资源所支持的功能。如果OPTIONS请求的URI是个星号（`*`），那么请求的是整个服务器所支持的功能。如果OPTIONS请求的URI是个实际的资源地址，那么请求的就是特定资源的可用特性。如果OPTIONS请求成功，客户端会收到一个包含了各种首部字段的`200 OK`响应，这些首部字段就描述了服务器或特定资源所支持的各种可选特性。

HTTP/1.1在响应中唯一指定的首部字段是`Allow`，它描述了服务器或特定所支持的各种方法，例如：`Allow: GET, HEAD, PUT`。

## 参考资料
1. David Gourley, Brian Totty. *HTTP: The Definitive Guide*. O'Reilly Media, 2002.
