title: (每周爆栈精选)如何向一个只有六岁的小孩解释什么是javascript闭包
date: 2013-10-19 17:23:59
tags: [old blog, javascript, closure]
---

这是我2年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/16546854201391952359197/)

来自爆栈[这里](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

首先有人抱怨这个：

>“Like the old Albert Einstein said: "If you can't explain it to a six-year old, you really don't understand it yourself.”. Well, I tried to explain JavaScript closures to a 27-year old friend and completely failed.
>How would you explain it to a 6-year old person that is strangely interested in that subject? ”

这位楼主大概意思是：

>爱因斯坦说过：“如果你不能向一个六岁大的小孩解释清楚，那么你自己并不完全领悟”，好吧，当我向一个27岁的朋友尝试解释关于javascript中的闭包，尼玛他完全不懂。那还能如何指望一个六岁屁大的孩子能够听懂？

 楼下一位非常有想象力的码农贴出了以下：

很久以前：

有一位公主...

```javascript
function princess() {
    // 她住在一个充满其妙冒险的世界。她遇到一个非常有魅力的王子，骑着一个独角兽，她还遇到巨型战龙，甚至还有会说话的动物等不可思议的事情。
    var adventures = [];

    function princeCharming() { /* ... */ }

    var unicorn = { /* ... */ },
        dragons = [ /* ... */ ],
        squirrel = "Hello!";

    // 但是，这个小公主每次都不得不返回到现实世界，变成一个普通人，还要面对各种无趣的大人们。
    return {

        // 她每一次回来，总会告诉这些大人，她所遇到各种非常令人惊讶的事情
        story: function() {
            return adventures[adventures.length - 1];
        }
    };
}
```

但是，大人们只是看到的是一个天真无邪的小女孩说...

```javascript
var littleGirl = princess();
...这些只会发生在童话中的故事
littleGirl.story();
```

但是他们不会相信独角兽和龙，因为他们永远看不到这些怪兽。大人们觉得这些只是存在小女孩的想象中。
但是我们都知道，其实这些都是真实存在...
