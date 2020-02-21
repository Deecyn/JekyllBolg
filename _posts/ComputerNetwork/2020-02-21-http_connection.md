---
layout: post
title: "HTTP 中的长连接与短连接"
subtitle: '在不同版本的 HTTP 协议中，长连接与短连接是怎么回事？有何区别？Connection 与 Proxy-Connection 头部有何关联？'
author: "Deecyn"
catalog: true
header-style: text
tags:
  - 计算机网络
  - HTTP
---

HTTP 长连接的本质是 **TCP 连接的复用**。

在HTTP/1.0 中，建立一个 HTTP 连接的同时，也会建立一个 TCP 连接用于传输数据。HTTP/1.0 版本中的 TCP 连接只能发送一个 HTTP 请求，发送数据完毕，就关闭连接。如果该需要请求其它资源，就必须再新建一个连接。

然而 **TCP 连接的新建成本比较高**，因为需要客户端与服务器的三次握手，并且开始时发送速率较慢（[Slow start](https://baike.baidu.com/item/%E6%85%A2%E5%90%AF%E5%8A%A8/8242395)）。所以，HTTP/1.0 版本的性能比较差，且随着网页需要加载的外部资源（CSS 文件、JS 文件、图片、超链接资源等）越来越多，这个问题就越来越突出了。

在HTTP/1.1 版本中，引入了**长连接**（persistent connection，或者叫持久连接）机制。在客户端发起 HTTP 请求时，声明头部：

```bash
Connection: keep-alive
```

表示请求长连接，要求服务器不要关闭 TCP 连接，以便其它请求复用。且 HTTP/1.1 默认是支持长连接的，不用额外声明 `Connection: keep-alive` ；而 HTTP/1.0 默认是不支持长连接的，所以 keep-alive 主要用于在 HTTP/1.0 版本的客户端和服务器中协商采用长连接。

若服务器同样响应 `Connection: keep-alive` ，则一个可以复用的 TCP 连接就建立了，直到客户端或服务器发送 主动关闭连接（或客户端发送 `Connection: Close` 要求服务器关闭 TCP 连接）。

Connection 属于**逐跳首部**（Hop-by-hop Header），只对单次转发有效，会因通过缓存或代理而不再转发。

互联网上，仍存在着大量简陋并陈旧的代理服务器在继续工作，它们很可能无法识别 Connection 头部。而代理服务器遇到无法识别的 Header，往往都会选择继续转发。大部分情况下这样做是对的，因为有很多使用 HTTP 协议的应用软件扩展了 HTTP 头部，如果代理服务器不传输扩展字段，这些软件将无法正常工作。

然而对 Connection 头部的转发违背了开始的设计原则，会带来新的问题。当陈旧的代理服务器将无法识别的 `Connection: keep-alive` 头部转发到目标服务器时：

- 若目标服务器也无法识别 Connection 头部，则表示此为过时的服务器和 HTTP 版本，不支持长连接，进而退化为短连接的方式。
- 若目标服务器可以识别，但并不会知道 keep-alive 是由代理错误的转发而来，它会认为是代理希望建立长连接，服务器同意之后也返回一个 keep-alive。同样，响应中的 keep-alive 也会被代理原样的转发给客户端，同时代理还会等待服务器关闭连接（实际上服务器会按照 keep-alive 的指示保持连接）。当浏览器收到 keep-alive 之后，会复用之前的连接发送其它请求；而陈旧的代理不会认为这个连接上还有其它请求，已经 close，发送的请求会被忽略（或者代理服务器报错）。这样，客户端浏览器和服务器会一直处于挂起状态，直到超时，既降低了性能也浪费了资源。

如图：![Connection_keep-alive](http://qn-img.deecyn.com/http%2FConnection_keep-alive.png)

此时，采用将请求头中的 Connection 替换为 **Proxy-Connection** 的方式，则：

- 短连接：浏览器 --> 陈旧的代理服务器，无法识别该头部 --> 转发 --> 目标服务器，不认识 Proxy-Connection --> 关闭 close。
- 长连接：浏览器 --> 新的代理，可以识别 --> 转发 Connection --> 目标服务器，识别 --> 响应 Connection。

显然，如果浏览器并不知道连接中有陈旧代理的存在，或者在陈旧代理任意一侧有新代理的情况下，这种方案仍然无济于事。所以有时候服务器也会选择彻底忽略 HTTP/1.0 的 keep-alive 特性：对于 HTTP/1.0 的请求，从不使用长连接，也从不返回 keep-alive。

浏览器对代理请求头的修改，是为了尽可能的兼容网络中可能出现的各种不规范的中转设备，使网络更健壮。

-----

（完）