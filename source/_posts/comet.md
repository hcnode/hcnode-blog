title: Comet 技术总结以及相关方案优劣对比
date: 2012-10-31 17:34:13
tags: [old blog, comet]
---

这是我三年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/16546854201293153047762/)，因为这篇blog，我有幸被邀请到[2012年的Velocity大会](http://velocity.oreilly.com.cn/2012/index.php?func=slidesvideos)作为[演讲嘉宾](http://velocity.oreilly.com.cn/2012/index.php?func=session&id=27)。


本文是关于comet技术的介绍，以及网易邮箱在使用comet技术中的经验介绍和各种解决方案优劣对比。

### 什么是comet
Comet是一种基于web的服务器端主动推送消息给浏览器端的技术，在传统的web应用，浏览器和服务器之间的通讯，是通过“浏览器请求-服务器端返回”这个过程服务器端被动的返回信息，Comet则是服务器端主动推送消息给浏览器端，这种方式可以及时将消息传送浏览器，所以对于一些对于消息处理需要很及时的web应用就会使用到comet技术，例如：webim

### Socket协议和http协议
socket包括有Flash XMLSocket、activex组件等浏览器插件，Flash XMLSocket的实现是在 HTML 页面中内嵌入一个使用了 XMLSocket 类的 Flash 程序，JavaScript 通过调用此 Flash 程序提供的套接口接口与服务器端的套接口进行通信，不过使用Socket协议的方式没有 HTTP 隧道功能，不能自动穿过防火墙，所以建议使用http协议的方式，本文也只讨论http协议的方式。

### comet的实现方式

#### long-polling：长轮询
long-polling方式的特点有以下：

 - 服务器端会阻塞请求直到有数据传递或超时才返回。
 - 客户端 JavaScript 响应处理函数会在处理完服务器返回的信息后，再次发出请求，重新建立连接。
 - 当客户端处理接收的数据、重新建立连接时，服务器端可能有新的数据到达；这些信息会被服务器端保存直到客户端重新建立连接，客户端才会把当前服务器端所有的信息取回。

long-polling的可选择载体：

 - 浏览器原生xhr
 - Jsonp（以html的script标签方式请求）
 - Flash xhr（随便跨域，无视浏览器兼容）
 - iframe（iframe页面方式请求）

long-polling优缺点：

**优点包括：**实现稍微简单，可供选择的载体多，并且浏览器兼容性好
**缺点包括：**轮询的方式定时请求，有数据返回后断开重新请求，这种方式消息到达可能不及时，以及浏览器端不断建立请求，导致过多资源，某些载体方式不支持post

#### Streaming：流方式

Streaming方式的特点有以下：

使用这种方式，每次数据传送不会关闭连接，连接只会在通信出现错误时，或是连接重建时关闭（一些防火墙常被设置为丢弃过长的连接， 服务器端可以设置一个超时时间， 超时后通知客户端重新建立连接，并关闭原来的连接）。
Streaming的可以选择载体：

 - 浏览器原生xhr
 - Flash xhr（随便跨域，无视浏览器兼容）
 - iframe（iframe页面方式请求）

Streaming优缺点：

**优点包括：**消息到达及时，浏览器端只需要一次请求，服务器端即可实现多次推送，并且get或者post方式都无压力
**缺点包括：**无

#### 邮箱的pushmail实现方案对比
旧方案：

 - 长连接方式:long-polling
 - 载体：jsonp
 - 优点：实现简单，兼容性好，可以跨域
 - 缺点：只能get方式请求，jsonp方式不能实现Streaming，容易请求中断，firefox无法隐藏加载提示

新方案：

 - 长连接方式: Streaming
 - 载体：flash xhr
 - 优点：没有任何兼容性问题，可以使用Streaming的方式推送，可以跨域，可以使用post方式请求
 - 缺点：部分没有安装flash的用户无法使用，经过统计，这部分用户只占1%左右，这部分可以改用旧方案

### 总结
通过各种方案对比，streaming+flash xhr是最佳的选择。

 - 跨域请求：新的方案可以轻松实现跨域请求，不再为跨域烦恼。
 - 浏览器兼容：使用flash的xhr可以无视各种浏览器的原生xhr的差别。
 - 使用streaming方式：可以避免反复数据返回-断开重连的情况下对客户端运行效率问题。