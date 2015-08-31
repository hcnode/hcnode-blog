title: (每周爆栈精选)不同环境中的事件句柄中的return false区别
date: 2013-10-18 17:21:05
tags: [old blog, dom, event, javascript]
---

这是我2年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/1654685420139195215454/)

来自爆栈[这里](http://stackoverflow.com/questions/1357118/event-preventdefault-vs-return-false)


引出问题的是楼主问jquery环境下preventDefault和return false的区别。

楼下有一位总结关于return false在各个环境下的区别，以下是节选和总结：

 - 在标准的DOM2 handler下(也就是使用addEventListener方式监听)，return false不会做任何事情（也就是不会阻止事件，也不会停止冒泡）

 - 在Microsoft DOM2 handler下（也就是使用attachEvent方式监听），return false会阻止事件，但是不会停止冒泡

 - 在标准的DOM0 handler下（也就是在html结构里面事件中return，如：```<a onclick="return ...">```）,return false会阻止事件，但是不会停止冒泡

 - 以上都是原生方式return false，都是不能默认阻止事件冒泡

 - 但是在jquery环境下监听事件（如：$('a').click(...)）return false会同时阻止事件和停止事件冒泡。

 