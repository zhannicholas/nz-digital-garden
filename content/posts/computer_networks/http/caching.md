---
date: "2020-12-13T16:41:19+08:00"
title: "HTTP：缓存"
authors: Nicholas Zhan
categories:
  - HTTP
tags:
  - HTTP
draft: false
toc: true
---

Web缓存指的是一些可以自动保存常用文档副本的HTTP设备。当Web请求经过缓存时，如果缓存设备本地存在一个可用的“已缓存”副本，就不需要再去**源服务器（origin server, 即持有资源实体的服务器）**获取这个文档了，直接将缓存设备本地存储中的文档副本返回给客户端即可。缓存具有不少优点：
* 减少冗余的数据传输，从而节省网络费用。
* 缓解网络瓶颈问题。有了缓存之后，可以更快地加载页面而不需要更多的带宽。
* 降低对源服务器的要求。服务器不仅可以更快地响应，还能避免过载的出现。
* 降低距离时延。因为从较远的地方加载页面会更慢。

## 缓存命中与不命中
缓存是很有用的，但一个缓存也不可能存储下世界上所有文档的副本。

当请求到达缓存时，若缓存中存在被请求内容的副本（副本应该是有效的），则可以直接将副本返回给客户端，这种情况被称为**缓存命中（cache hit）**。否则，缓存中没有可用副本，请求会被转发给源服务器，这种情况被称为**缓存不命中（cache miss）**。源服务器上的内容可能会发生变化，缓存需要不时地对其进行检测，看看它们保存的副本是否仍然是服务器上的最新内容，这些*新鲜度检测（freshness checks）*就被称为**HTTP再验证（HTTP revalidation）**。

![Cache hits, misses, and revalidations](/images/computer_networks/http/cache-hits-misses-and-revalidations.png)

### 再验证
HTTP定义了一些特殊的请求，它们可以不从服务器获取整个对象，并快速检测出内容是否依然是新鲜的。缓存可以在任意时刻，以任意频率对副本进行再验证。由于网络带宽非常珍贵，大部分缓存只有在客户端发起请求并且副本旧得足以需要检测时，才会对副本进行再验证。

缓存在对副本进行再验证时，会向源服务器发送一个小的再验证请求。如果内容没有发生变化，服务器就会以 `304 Not Modified` 进行响应。只要缓存确认了副本仍然是有效的，它机会再次将副本标记为新鲜的并将副本提供给客户端，这被称为**再验证命中（revalidation hit）**或**缓慢命中（slow hit）**。因为这种方式需要与源服务器进行核对，所以比单纯的缓存命中要慢，但它没有从服务器获取对象数据，所以比缓存不命中要快一些。

![Successful revalidations and failed revalidations](/images/computer_networks/http/successful-revalidations-and-failed-revalidations.png)

HTTP提供了一些再验证缓存对象的工具，其中最常用的就是 `If-Modified-Since` 首部。当它被添加到GET请求中时，它告诉服务器只有当对象的副本被缓存并且对象已经被修改后，才发送对象。当服务器收到GET If-Modified-Since请求时，可能处于三种状态:
1. 服务器内容未修改（再验证命中）。服务器向客户端发送 `304 Not Modified` 作为响应。
2. 服务器内容已修改但未删除。这是服务器对象与缓存副本已经不同了，服务器会向客户端发送一条带有完整内容的普通 `200 OK` 作为响应。
3. 服务器对象已被删除。这个时候服务器会回送一个 `404 Not Found` 响应，缓存收到后会将对应的副本删除。

## 缓存的处理步骤

![Processing a fresh cache hit](/images/computer_networks/http/processing-a-fresh-cache-hit.png)

Web缓存对一条HTTP GET报文的处理过程包括以下7个步骤：
1. 接收——缓存从网络中读取抵达的请求报文。
2. 解析——缓存对报文进行解析，提取出URL和各种首部。
3. 查询——缓存查询本地是否存在可用副本，如果没有就从服务器获取一份并保存在本地。
4. 新鲜度检测——缓存查看已缓存的副本是否足够新鲜，如果不是，就询问服务器该内容是否有更新。
5. 创建响应——缓存使用新的首部和已缓存的主体来构建一条响应报文。
6. 发送——缓存将响应报文发送给客户端。
7. 日志——缓存向日志文件中新增一个描述这个事务的条目（可选）。

下面的流程图展示了缓存是如何处理GET请求的：

![Cache GET request flowchart](/images/computer_networks/http/cache-get-request-flowchart.png)

## 保持副本的新鲜
HTTP提供了一些机制用来保持已缓存数据与服务器数据之间的一致性。这些机制被称为**文档过期（document expiration）**和**服务器再验证（server revalidation）**。

### 文档过期
通过HTTP的 `Cache-Control` 和 `Expires` 首部，源服务器可以为每个文档附加一个“过期时间”，从而说明在多长时间内可以认为这些内容是新鲜的。

在缓存文档过期之前，除非客户端请求中包含阻止提供已缓存或未验证资源的首部，缓存可以不与服务器联系并任意使用这些副本。一旦文档过期，缓存就必须与服务器进行核对。

服务器用HTTP/1.0+的 `Expires` 首部或HTTP/1.1的 `Cache-Control: max-age` 首部来指定过期时间。二者所做的事情本质上是一样的，区别在于：前者使用绝对时间，后者使用相对时间。

### 服务器再验证
已缓存文档过期了并不意味着它与源服务器上目前处于活跃状态的文档有本质区别，这只是意味着到了核对时间了，这种情况就是*服务器再验证*，缓存需要询问源服务器文档是否发生了变化：
* 若再验证显示内容发生了变化，则缓存需要获取一份新的副本，替换掉旧的数据，然后发送给客户端。
* 若再验证显式内容没有变化，则缓存只需要获取新的首部（首部中包含了新的过期日期）并更新缓存中的首部。

#### 使用条件方法进行再验证
HTTP的条件方法可以高效地实现再验证。HTTP允许缓存向源服务器发送一个“条件GET”，请求服务器只有在文档与缓存中的文档副本不同时，才回送对象主体。通过这种方式，单个条件GET就能完成新鲜度检测和对象获取。要发起条件GET请求，需要向GET请求报文的首部中加入以下特殊的条件首部，Web服务器只有在条件为真时才返回对象。

和缓存相关的条件首部有两个：
1. `If-Modified-Since: <date>`：如果文档在指定日期 `<date>` 之后被修改了，就执行请求的方法。它一般和 `Last-Modified` 响应首部配合使用，只有当内容被修改之后才去获取新的内容。
2. `If-None-Match: <tags>`：服务器可以为文档提供特殊的标签（`ETag`），这些标签就像序列号一样，如果已缓存标签与服务器文档中的标签不同，就执行请求方法。

## 控制缓存
HTTP为服务器定义了几种声明文档在过期前可以被缓存多长时间的方式。按照优先级递减的顺序，服务器可以：
* 附加一个 `Cache-Control: no-store` 首部到响应中去。
* 附加一个 `Cache-Control: no-cache` 首部到响应中去。
* 附加一个 `Cache-Control: must-revalidate` 首部到响应中去。
* 附加一个`Cache-Control: max-age` 首部到响应中去。
* 附加一个 `Expires` 首部到响应中去。
* 不附加过期信息，让缓存自己确定过期时间。

### `no-store`与`no-cache`
HTTP/1.1提供了几种标记对象不可被缓存的方式。从技术上说，这些不可缓存的页面永远都不应该被存储在缓存中。下面就是几个标记文档不可被缓存的首部：
```
Pragma: no-cache
Cache-Control: no-cache
Cache-Control: no-store
```
`no-cache` 并不意味着"don't cache"，当响应被标记为 `no-cache` 时，缓存在与源服务器进行新鲜度再验证之前，不能将其提供给客户端。标记为   `no-store` 的响应禁止缓存保持响应的副本。HTTP/1.1中的 `Pragma: no-cache` 首部的目的是兼容 HTTP/1.0+。

### `must-revalidate`
`must-revalidate` 并不意味着"must revalidate"。`Cache-Control: must-revalidate` 响应首部告诉缓存，当本地资源的存活时间没有超过 `max-age` 时，可以直接返回。否则，必须跟源服务器进行再验证。

### `max-age`
`Cache-Control: max-age` 首部表示从服务器将文档传来之时起，可以认为该文档处于新鲜状态的秒数。还有一个 `s-maxage` 首部，其行为与 `max-age` 类似，但只用于共享（公有）缓存：
```
Cache-Control: max-age=3600
Cache-Control: s-maxage=3600
```
服务器可以将 `max-age` 或 `s-maxage` 的值设置为 0，从而要求缓存不缓存某个文档或每次访问该文档时都刷新：
```
Cache-Control: max-age=0
Cache-Control: s-maxage=0
```

### `Expires`
`Expires` 响应首部已被弃用，因为它声明的是实际的过期时间而不是相对时长。当机器的时钟不同步时，会有问题。

## 参考资料
1. David Gourley, Brian Totty. *HTTP: The Definitive Guide*. O'Reilly Media, 2002.
