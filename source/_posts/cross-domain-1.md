title: 前端开发各种cross之cross domain(一)
date: 2012-05-10 11:52:29
tags: [old blog, javascript, crossdomain]
---

这是我三年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/165468542012410115229581/)

作为一个苦逼前端开发工程师，不得不面对各种cross，比如面对五花八门的浏览器我们必须cross browser，面对各种终端，我们必须cross device，在这么多年的前端开发经历中，在不同的域之间穿越中，遭受各种折磨，所以这次和大家分享的是cross domain。

这次分享的cross domain，是包括所有跨域调用，无论是跨域获取数据，还是跨域跨frame调用，所以会分为两部分，这次会先分享跨域获取数据，跨域获取数据大概有以下方式。

### jsonp方式
这是最简单，也是最实用的跨域获取数据方式，原理是在浏览器端通过生成script标签，并通过js callback的形式实现跨域访问，比如一个jsonp接口是这样：

    http://mail.163.com/someapp/jsonp?somequery=xxx&callback=fSomeMethod
然后服务器端会通过以下方式返回数据：

    fSomeMethod({返回的json数据对象})

 - 优点：实现简单，使用灵活，并且原生cross all browser
 - 缺点：只能get方式获取数据，某些浏览器会显示加载中提示

### 表单post方式
这个方式会复杂一点点，原理是post表单到一个隐藏的iframe，然后iframe将数据post回来同域的一个url，这时候就可以直接调用同域的回调：

 - 首先post表单到如：

    http://other.domain.com/someapp?somequery=xxx&callback=fSomeMethod&backurl=http://mail.163.com/proxy

 - 这个接口返回的内容:

```html
<form action="${backurl}" method="post">
 <input type="hidden" value="返回的数据">
 <input type="hidden" name="callback" value="${callback}">
</form>
<script>document.getElementsByTagName("FORM")[0].submit();</script>
```

 - 然后post到http://mail.163.com/proxy 后，返回：

```html
<script>
parent.${param.callback}("返回的数据")
</script>
```
这样就完成整个跨域获取数据的过程
**优点：**支持post方式，并且原生cross all browser
**缺点：**实现有点复杂，并且流程有点曲折，需要两次请求，而且表单post方式会引起刷新提示的问题

### 服务器代理
服务器代理方式跨域调用也是使用的比较广泛的方式，原理就是在服务器端来获取跨域数据，然后在同域里ajax方式或者其他方式返回给浏览器。
**优点：**客户端实现简单，没有cross browser问题
**缺点：**需要在服务器端实现模拟请求获取数据

### html5的XDomainRequest
终于在html5迎来了对跨域的ajax，泪牛满脸啊，这下完全可以通过浏览器的原生方式跨域ajax获取数据，这里一个对浏览器的各种跨域ajax的测试：
http://www.debugtheweb.com/test/teststreaming.aspx
**优点：**浏览器原生支持跨域方式ajax请求
**缺点：**只能在支持html5的高级浏览器中支持

### flash request
这个是目前跨域请求最好的一个解决方案，建议在不能用jsonp的方式时候，都可以使用flash request方式，而且flash request可以配置一个安全策略，可以允许哪些域可以调用，然后被跨域的调用需要配置一个crossdomain.xml，允许可以被那个域的flash跨域调用，内容如下：

```xml
<?xml version="1.0"?>
<!DOCTYPE cross-domain-policy SYSTEM "http://www.adobe.com/xml/dtds/cross-domain-policy.dtd">
<cross-domain-policy>
 <allow-access-from domain="www.xxx.com" />
</cross-domain-policy>
```

这种方式的局限就是必须依赖flash，我们这边在邮箱曾经做过一个统计，99%的用户可以通过这种方式获取到数据，另外1%的用户可能是没有安装flash或者flash版本有问题。
 - 优点：依赖flash下可以无视各个浏览器兼容问题
 - 缺点：正所谓成也萧何败也萧何，缺点也是因为要依赖flash

以上是获取数据时需要cross domain的解决方案，下一篇是跨frame时候需要cross domain的分享。