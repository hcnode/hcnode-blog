title: (每周爆栈精选)javascript中“==”和“===”的性能对比
date: 2013-10-19 17:21:52
tags: [old blog, javascript]
---

这是我2年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/16546854201391952152914/)

来自爆栈[这里](http://stackoverflow.com/questions/359494/does-it-matter-which-equals-operator-vs-i-use-in-javascript-comparisons)

以下是原文回答精选

条件判断中的“===”操作符，相当于“==”操作符中没有类型转换的条件对比，并且类型必须是一样的。

“==”操作符在判断是否相等之前会进行类型转换，“===”操作符则不会做任何类型转换，所以如果两个值的类型不一样，那么===会返回false，所以当这种情况下===效率会更加快，并且返回的结果可能会和==不一样，如果类型一样的话，则性能也将会一样。

引用javascript大师Douglas Crockford的《JavaScript: The Good Parts》：
>javascript中有两组相等判断的操作符：===、!==，和另外一个SB组合 ==、!=。如果两边的对象是相同的类型，并且是相同的值，那么===返回true，!==返回false，SB组合也是一样会返回同样的结果，但是如果他们不是一样的类型，那么会尝试强制转换类型，然后再进行比较，至于转换规则，是异常的复杂和坑爹。

下面有些有趣的例子：

```javascript
'' == '0'           // false
0 == ''             // true
0 == '0'            // true

false == 'false'    // false
false == '0'        // true

false == undefined  // false
false == null       // false
null == undefined   // true

' \t\r\n ' == 0     // true
```

我的建议是不再使用SB组合，而是要使用===和!==。以上的例子，将==替换成===都将会返回false。

关于性能的对比测试
回到主题，以下这个例子，我将原来的改了一下，[测试](http://jsperf.com/comparison-of-comparisons/16)这两种组合的效率，结果一定会使你和你的小伙伴们都惊呆了。
