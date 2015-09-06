title: javascript设计模式之“Observer”模式在网易邮箱中实践和分享
date: 2012-11-08 17:35:28
tags: [old blog, javascript, Observer]
---

这是我三年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/1654685420121085355140/)


### 为什么要使用Observer模式
在大型的ajax项目前端开发中，模块分得很细，多个开发者负责不同的业务逻辑模块开发，会需要不同模块之间互相调用接口，在网易邮箱的前端开发中遇到过这样的情况也是家常便饭，虽然我们可以将公共调用的功能分离出来到一个“公共库”，然后开发者需要实现某个功能时，可以去“公共库”找相关的接口，然后在自己的代码中调用。

但是如果接口有变更、或者新需求需要增加调用，就导致各个模块负责人耦合维护，这种方式在小型的项目没有大问题，不过项目越做越大后，就会导致模块之间耦合越来越大，维护成本越来越高，在新的网易邮箱版本里面，使用了Observer的设计模式实现模块之间的解耦。

### Observer模式的定义
Observer（观察者模式）：定义对象间的一种一对多的依赖关系,以便当一个对象的状态发生改变时,所有依赖于它的对象都得到通知并自动刷新。

### 举例说明
先举个例子：邮箱中的收件箱模块和读信模块，分别是程序员A和程序员B负责，在旧的模式里，收件箱读取一封新邮件后，B会调用A的接口.setRead(sMailId)实现去掉该邮件未读标记，然后有一天，负责左侧导航的同事C说：产品增加一个需求，读取新邮件后，左侧导航的收件箱的未读数需要更新。这时，C发给B一个更新导航的接口，然后B就要修改代码，加上C提供的接口.updateNav()。

 - 使用Observer模式后：

首先定义了一个afterRead的事件，收件箱模块监听了这个事件：Listener.listen("afterRead", DoSetReadFunction)，新邮件读取之后，会触发一个事件：Listener.fire("afterRead")，这时，监听了这个事件的收件箱模块会调用接口，然后在新的需求里，C同事只需要监听：Listener.listen("afterRead", DoUpdateNavFunction)，就实现了左侧导航的新邮件数更新了。

 - 这个过程的变化相当于：
**旧的方式：**读取一封新邮件后 -> 调用收件箱接口 -> 调用导航接口
**新的方式：**读取一封新邮件后 -> 告诉所有模块：我刚读了一封新邮件 -> 其他监听了这个事件的模块被通知了 -> 调用相关接口

 - 简单的自定义事件实现

```javascript
var Listener = {
 listen   : // 实现自定义事件监听
 unlisten  : // 解除自定义事件监听
 unlistenAll  : // 解除所有自定义事件监听,
 fire   : // 触发事件发生
 _events  : // 相关监听的事件以及事件监听的句柄
};

// _events的结构大致如下：
_events : [
 {
  name : "event0", // 自定事件0
  handlers : [
   function(){...},// 监听的句柄
   function(){...} // 监听的句柄
  ]
 },
 {
  name : "event1", // 自定事件1
  handlers : [
   function(){...},// 监听的句柄
   function(){...} // 监听的句柄
  ]
 }
]
```

上面的简单的自定义事件实现方式，就可以很好的解决模块之间耦合的问题，当然也可以做得功能更加强大，比如事件触发可以加上参数支持、实现调用句柄获取当前的event对象等。