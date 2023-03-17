---
author: Nicholas Zhan
title: "浏览器的同源策略"
date: 2023-03-10T21:38:42+08:00
draft: true
language: zh-cn
featured_image:
summary: 浏览器的同源策略（same-origin policy）是保障 Web 安全的一种重要机制，它规定了在一个源中加载的文档（document）或脚本（script）与来自不同源的其它文档或脚本进行交互的方式。只有同源的文档或脚本之间才可以相互交互。
categories:
  - Web
tags:
  - Web
---

浏览器的同源策略（same-origin policy）是保障 Web 安全的一种重要机制，它规定了在一个源中加载的文档（document）或脚本（script）与来自不同源的其它文档或脚本进行交互的方式。只有同源的文档或脚本之间才可以相互交互。

同源策略帮助网站隔离了可能包含恶意代码的网页，保护用户免受恶意攻击。如果没有同源策略，攻击者可以通过发送包含跨域请求的恶意脚本，从而窃取用户的数据或使用用户的身份验证信息。 同源策略是阻止此类攻击的第一线防御措施。

例如：当你在浏览器中打开两个不同的页面时，它们可能会尝试通过 JavaScript 进行通信，但由于同源策略，这些页面无法访问彼此的 Cookie，缓存，localStorage 等资源。这样用户的信息就得到了有效的保护。

## 同源

互联网上的很多网站都有自己的域名，比如：
* www.example.com
* www.google.com
* www.facebook.com

这些网站被称为“源”。同源是针对文档或脚本 [URL](https://en.wikipedia.org/wiki/URL) 来说的，如果两个 URL 的协议、主机和端口均相同，那么这两个 URL 同源。
假设我们有一个 URL 是：`http://www.example.com:80/dir/page.html`，下面给出来了一些是否与它同源例子：

| URL | 是否同源 | 说明 |
|-----|--------|------|
| http://www.example.com/dir2/other.html | 是 | 协议、主机和端口相同 |
| http://username:password@www.example.com/dir/other.html | 是 | 协议、主机和端口相同 |
| http://example.com/dir2/other.html | 否 | 协议、端口相同；主机不同 |
| http://www.example.com:8000/dir/other.html | 否 | 协议、主机相同；端口不同 |
| http://en.example.com/dir/other.html | 否 | 协议、端口相同；主机不同 |
| https://www.example.com/dir/page.html | 否 | 主机相同；协议和端口不同 |

## 跨域

跨域请求发生在以下情况：
* 协议不同（如http、https）
* 域名不同（如example.com、google.com）
* 端口号不同（如80、8080）

跨域请求存在风险，因为它可能会导致网站被黑客攻击。但是，在某些场景下我们也需要进行跨域请求，例如通过 AJAX 技术从服务器获取数据。在这种情况下，可以使用 CORS（跨域资源共享）或 JSONP 等技术来进行更安全地跨域通信。

下面这段代码试图从 https://myanimelist.net/character.php 加载数据，但是这个请求会被浏览器拒绝：
```html
<html>
<body>
<h1>Characters:</h1>
</body>
<script>
    let url = `https://myanimelist.net/character.php`;
    fetch(url).then(function (response) {
        // The API call was successful!
        console.log(response.text());
    }).catch(function (err) {
        // There was an error
        console.warn('Something went wrong.', err);
    });
</script>
</html>
```
使用 F12 打开浏览器的开发者工具，你会发现有一大段红色的错误信息：

![Same Origin Policy Example](/images/web/same_origin_policy_example.png)

这段错误信息说明，浏览器发现 `http://localhost:63342` 与 `https://myanimelist.net//character.php` 不同源，这违反了同源策略，可能出现安全问题，所以拒绝了该请求。
但是，错误信息也给了我们一些提示：“If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.”
根据提示，我们改造代码，在 使用 `fetch()` 的时候加入一个参数：`{mode: "no-cors"}`，错误就会消失：
```html
<html>
<body>
<h1>Characters:</h1>
</body>
<script>
    let url = `https://myanimelist.net/character.php`;
    fetch(url, {mode: "no-cors"}).then(function (response) {
        // The API call was successful!
        console.log(response.text());
    }).catch(function (err) {
        // There was an error
        console.warn('Something went wrong.', err);
    });
</script>
</html>
```

