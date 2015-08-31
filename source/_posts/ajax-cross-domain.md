title: 跨域Ajax问题总结
date: 2013-06-27 15:50:12
tags: [old blog, ajax]
---

这是我2年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/1654685420135273483575/)


最近在用xhr2做某个项目的后台，在这过程中遇到各种疑难杂症，对比起来，flash request的跨域其实是既简单又实用，而xhr2则有各种限制。

### 先来对比flash request和xhr2的跨域策略区别

 - flash request：使用配置xml "crossdomain.xml"，来进行配置，不需要修改其他比如返回http头，flash会先获取这个xml查询http请求，是否有权限进行跨域。

 - xhr2：使用浏览器自带的xmlhttprequest level2来进行跨域ajax请求，浏览器和服务器需要发送和返回对应的http头来进行跨域策略。

### 下面是在用到xhr2是遇到的问题：

 - 首先遇到第一个问题，因为extjs框架进行跨域访问时，会自动带上自定义头：X-Requested-With，这时候浏览器会提示出错，原因是因为当有自定义头时，服务器端需要返回头：Access-Control-Allow-Headers:X-Requested-With，表示允许可以设置请求自定义头

 - 第二个问题是发现跨域请求没有带上cookie，原因是因为在extjs的xhr2，withCredentials默认为false，需要加上这个属性请求才能带上cookie，但是加上withCredentials属性后发现还是出错，原因是当withCredentials为true时，返回的Access-Control-Allow-Origin的值不能为*，需要为一个特定的域名，最后把这个改为测试机的域名后，以为终于可以了吧，Holy Crap！还是不行，好吧，原因是服务器端http头也要返回Access-Control-Allow-Credentials:true

### 总结：

 - 当xhr2跨域ajax时，不需要带上cookie，没有自定义头，只需要返回设置Access-Control-Allow-Origin头就可以。

 - 如果需要设置自定义头，需要服务器端返回Access-Control-Allow-Headers:自定义头。

 - 如果需要带上cookie，则xhr2对象需要设置withCredentials为true，并且服务器返回Access-Control-Allow-Credentials:true ，并且Access-Control-Allow-Origin不能为*