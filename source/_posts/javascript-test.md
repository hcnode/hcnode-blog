title: Javascript单元测试？和大神一起来品尝咖啡吧
date: 2015-07-02 15:26:37
tags: [node.js, test]
---

## 关于分享

本文是关于单元测试的分享，单元测试是在项目开发里面某个模块对某个接口或者函数进行测试检查，在服务器端开发这是非常常见的，但是在web前端开发，特别是js开发中，是比较少见的，所以对于各位前端开发工程师，单元测试一件非常痛苦的事情，所以我打算在这里跟大家分享一下，我所了解到一些比较新的前端的测试框架。
本文所有的例子都可以在最后的example的github地址里面找到
<!-- more -->

## js测试框架：mocha

最近几年因为node.js的发展，也带来了js相关技术的快速发展，其中就包括js的各种单元测试framework和assertion library，比如jasmine、mocha、karma等测试框架，这里其中包括一个非常优秀的框架：[mocha](http://mochajs.org/)，没错，就是“摩卡”咖啡。
什么是mocha？来看看它的featrues

- 虽然是node.js环境，但是它也支持浏览器环境
- 支持异步的测试用例，包括promise
- 支持代码覆盖率coverage测试报告
- fail的测试用例可以显示differ（暂时只限String）
- 支持配置进行不同的测试（比如使用什么断言库，显示reporter的方式）
- 使用任何你喜欢的断言库
- 多种reporter选择
- and more! ([查看更多featrues](http://mochajs.org/))

关于mocha的安装使用等，这里就不详细说了，可以直接官网看教程。
##assertion library

assertion library也就是断言库，前面介绍mocha说过，mocha可以选择第三方的断言库，比如jasmine、should、chai、或者直接使用node的assert。

- [jasmine](http://jasmine.github.io/)：包含丰富的语法、支持异步，我们webmai几年前也用过，它本身就是一个支持浏览器端的js测试框架。
- [should](https://github.com/tj/should.js)：assertion语句非常有可读性，写起测试代码简直就是写文章一样。
- [chai](https://github.com/chaijs/chai)：集成其他库的优点，支持should、expect、assert的各种写法习惯（第一次看到这个名字实在“cai”不出它的意思，后来查原来是：“印度奶茶”）。
- 其他：[node的assert](https://nodejs.org/api/assert.html)、jq的[qunit](https://qunitjs.com/)

##should.js

如果要我推荐一个断言库，我推荐should，为什么不用chai这样的“瑞士军刀”？
是因为mocha里面已经有“milk”而不需要milk tea的原因？
还是因为它看上去有点山寨的原因？
都不是！而是因为mocha和should的作者是js和github界大名鼎鼎的[TJ大神](https://github.com/tj)！
关于TJ大神的传说是在是太多了，既然说起他，我顺便列一下他神奇的features：

- Node 知名模块作者(Express, ejs, commander, connect, mocha, co, koa等等)
- 除js和node以外许多其他语言(C、java、go等)的高手
- 随便一个项目在 github 上 star 数量成百上千，github follower数量上万，且排名在前十(前十中有三位为github创始人, 还有linux的作者)
- 是一个90后，喜欢杀马特造型
- 从做设计入行，不看书，不用培训，不参加任何开发者组织和大会，通过看别人代码学习（[TJ亲自回答这位网友的疑问](http://www.quora.com/How-did-TJ-Holowaychuk-learn-to-program)）
- 他一天写的代码，相当于你一周写的代码量，而且代码质量更好更简洁，即使这样，他每天还有时间发20多条tweeter，在githut上回复所有的feedback，他另外还在一个在美国加州公司的全职工作。
- 因为他的代码高产，并且没有参加过任何大会，以致有人怀疑他不是一个人，而是一个组织，甚至[有人研究他](http://www.quora.com/How-is-TJ-Holowaychuk-so-insanely-productive)，从他以往的代码里面，尝试从他的字里行间寻找出他不是一个人的“破绽”

回到should.js，记得第一眼看到should的assert语法，当时觉得有点不可思议，比如一下：
```javascript
(5).should.be.exactly(5).and.be.a.Number;
var harry = {
    name : "harry",
    age : 18
};
harry.should.be.an.Object;
harry.should.have.property("name").and.have.property("age");
harry.name.should.be.equal("harry");
harry.age.should.be.equal(18);
```
所有变量不管什么类型，都可以直接使用should属性，我以为使用了某种对变量的封装注入属性，其实后来发现原理很简单，should的readme最后写的：

> OMG IT EXTENDS OBJECT???!?!@
> 
> Yes, yes it does, with a single getter should, and no it won't break
> your code, because it does thisproperly with a non-enumerable
> property.

OMG！原来是扩展Object的prototype！代码就是这么简单：

```javascript
Object.defineProperty(Object.prototype, 'should', {
    set: function(){},
    get: function(){
        return should(this);
    },
    configurable: true
});
```

另外一个让我觉得神奇的地方，是它们的channing语法，从它们提供的example看到should提供各种比如an/and/be/have等，我在想应该在什么时候使用be，什么时候使用have，如果我想判断是否是Object，那我应该使用should.be.an，还是should.be.a，但其实这些关键字的作用都是一样的，只是为了增加可读性才提供这些chaining words！

> chaining assertions
> 
> Every assertion will return a should.js-wrapped Object, so assertions
> can be chained. To help chained assertions read more clearly, you can
> use the following helpers anywhere in your chain: .an,.of, .a, .and,
> .be, .have, .with, .is, .which. Use them for better readability; they
> do nothing at all. For example:
> user.should.be.an.instanceOf(Object).and.have.property('name', 'tj');
> user.pets.should.be.instanceof(Array).and.have.lengthOf(4); Almost all
> assertions return the same object - so you can easy chain them. But
> some (eg: .lengthand .property) move the assertion object to a
> property value, so be careful.


对了，其实TJ大神在mocha之前曾经写过另外一个类似的js测试框架：[expresso](https://github.com/visionmedia/expresso)，不过他说，这个库不再维护，要么建议你使用mocha，要么你可以找大神自荐来维护这个库，expresso的意思是意式特浓咖啡，估计大神觉得咖啡太浓太苦了，所以加了些milk和chocalate，便成了mocha！

##自定义assertion

无论是should.js还是jasmine，除了使用它们已经定义好的assertion（比如equal,instanceOf），我们还可以自定义自己的assertion。

- jasmine: 提供教程如何[自定义matcher](http://jasmine.github.io/2.3/custom_matcher.html)。
- should.js: 没有提供具体的教程，不过Api文档显示提供[Assertion.add](http://shouldjs.github.io/#assertion-add)自定义Assertion， 在这之前了解一下配置[Assertion.params](http://shouldjs.github.io/#assertion-assert)，也可以通过看should.js的源码，可以知道怎么实现自定义的assertion，比如以下是[bool.js](https://github.com/tj/should.js/blob/master/lib/ext/bool.js)，可以看到怎么实现自定义的assertion

```javascript
module.exports = function(should, Assertion) {
    Assertion.add('true', function() {
        this.is.exactly(true);
    }, true);

    Assertion.alias('true', 'True');

    Assertion.add('false', function() {
        this.is.exactly(false);
    }, true);

    Assertion.alias('false', 'False');

    Assertion.add('ok', function() {
        this.params = { operator: 'to be truthy' };

        this.assert(this.obj);
    }, true);
};
```

比如使用"ok"：
```javascript
{}.should.be.an.ok;
```
下面我定义了一个判断email地址的domain assertion：
```javascript
Assertion.add('domain', function(domain) {
    this.params = { operator: 'to have domain ' + domain };

    this.obj.should.be.a.String; // 必须是一个string
    // 必须有@，而且不能在最前或者最后
    this.obj.indexOf("@").should.be.greaterThan(0).and.not.be.equal(this.obj.length - 1);
    this.obj.split("@")[1].should.be.equal(domain);
});
```	
使用“domain”：
```javascript	
"harry@163.com".should.be.the.domain("163.com");
```
##浏览器端使用mocha

前面说的都是在node.js环境下的单元测试，如果要在浏览器端进行测试，应该怎么做呢。
mocha本身是支持浏览器的测试，我们可以使用[bower](https://github.com/bower/bower)下载依赖，bower是一个浏览器端类似npm的一个库管理工具，可以管理库的依赖，包括库之间的依赖、以及版本管理，一般的js库，如果它们声称支持browser的话，基本都可以通过bower找到。
首先通过命令：

```bash
$bower install mocha should 
```

下载mocha和should到当前目录的bower_components文件夹里
在test目录下新建一个html，内容如下：

```html
<html>
<head>
<meta charset="utf-8">
<title>Mocha Tests</title>
<!--mocha和should库引用开始-->
<link rel="stylesheet" href="../bower_components/mocha/mocha.css" />
<script src="../bower_components/should/should.js"></script>
<script src="../bower_components/mocha/mocha.js"></script>
<!--mocha和should库引用结束-->
<!--初始化mocha，设置为bdd模式-->
<script>mocha.setup('bdd')</script>
<!--加入需要测试和测试用例的js-->
<script src="../assertion/assertion_email.js"></script>
<script src="assertion.test.js"></script>
</head>
<body>
    <!--测试报告容器-->
    <div id="mocha"></div>

</body>
<script>
    mocha.run(); // 启动
</script>
</html>
```
   然后用浏览器打开这个页面就可以运行测试并看到测试结果。 
##Karma

在前面的例子里面，使用mocha在浏览器运行测试，首先需要手动新建页面，然后手动加上对应的库和测试代码以及单元测试代码，但这些都不够智能和自动化，那么有没有一个框架可以做到将这些都自动化，有！就是[karma](https://karma-runner.github.io/0.12/index.html)！

karma是一个浏览器端的测试框架，它起源2012年google开源了Testacular，2013年Testacular改名为karma
下面列出karma非常实用的features：

- 可以运行在真实的浏览器，包括chrome、firefox、ie等
- 在不同的浏览器测试可以同时运行并且全自动
- 可以使用mocha或者jasmine等第三方测试框架
- 可以自动监视被测试的文件是否有修改，如果有，自动重新运行一遍
- 支持代码覆盖率报告，包括Statements、Branches、functions、lines四个覆盖率的显示
- 支持requireJs方式运行，这意味着node.js环境下的js库，可以不需要browserify打包，都可以直接在浏览器端运行

karma也是基于node环境，使用方式非常简单，可以使用<code>karma ini</code>命令方式，一步一步的选择比如使用的测试框架、测试的浏览器类型、测试用例的js文件等等，详细可以查看关于[配置的说明](https://karma-runner.github.io/0.12/config/configuration-file.html)，你也可以手动修改这个配置。

配置完后，使用<code>karma start</code>运行，默认会运行当前目录的karma.config.js配置，你也可以指定配置文件的位置，这样你就可以一个项目里面有多个测试配置文件，根据不同的需要，手动或者在grunt里面构建你需要的测试任务。
karma可以使用mocha和should，因为karma本身默认支持mocha测试框架，所以在config的file include里面，是不需要引用mocha的库（而是在配置加上 frameworks: ['mocha']），但是需要手动加上should库：

```javascript
 files: [
    "bower_components/should/should.js",
    "..."
   ],
```
##supertest

再介绍一款TJ大神的作品：supertest，这是一个用来测试http接口，因为都是同一个作者，所以它可以和轻易简单和mocha、should结合一起使用。
supertest的特点是，它是一个专门设计给http接口用的断言库
```javascript
request("http://some.base.url/")
.get('/someGetMethod')
.expect('Content-Type', /json/)
.expect('Content-Length', '20')
.expect(200)
.end(function(err, res){
    if (err) throw err;
});
```
上面的例子expect http的status、header、response等信息，这种方式测试http接口非常简单方便。
以上的例子没有使用should，如果要使用，在js最前面require("should")，就可以使用了：
```javascript
request('http://localhost:4001')
.get('/')
.end(function(err, res){
    res.status.should.equal(200);
    res.text.should.equal('hey');
    done();
});
```
不久前我在写一个基于restful接口的应用，当我看到大神的这个测试库后受到启发，写了一个基于supertest的restful测试框架，这个测试库的亮点就是可以关联各个请求之间的请求参数，有兴趣的可以[了解一下](https://github.com/hcnode/supertest-rest)。
总结

现在知道TJ大神的代码为什么这么高产了吧，正所谓工欲善其事，必先利其器，正是有了他开发的这些“利器”，他写起各种js库才会游刃有余。
可能对于大部分web前端开发工程师，我们不是TJ大神那样写纯碎的js库，我们面对的，可能是各种：

- 快速紧急的项目
- 不断修改需求导致不断修改
- 前端工程师不断加班加点赶项目
- “后天就要上线了，你觉得我还有时间做单元测试？”
- “重构的时间都没有，哪有时间写测试代码”
- “本来我的代码写得挺好挺规范的，但是后来各种非符合规范的需求，代码越撸越惨不忍睹”

另外一方面是web前端开发的工作太杂了，除了上述的问题，很多时候，比如遇到：
- js开发调试效率低
- 前后端联调不顺畅
- 前端涉及面广出现问题时候不利于排查
- 测试－发布－补丁流程不通畅等

其实现在js开发已经是可以做成相当规范了，从开发前的[yeoman](http://yeoman.io/)构建项目模版、到库的bower依赖管理、再到mocha或者karma的测试、再到grunt或者[gulp](http://gulpjs.com/)任务构建、甚至集成到[jenkins](https://jenkins-ci.org/)的CI服务，就像java项目可以有一整套完整规范的流程。


##Examples

前面介绍的mocha+should+karma，我这里写了一个完整的例子。
``` bash
git clone https://github.com/hcnode/mocha-should-karma-examples﻿
```
下载源码后，进入目录，然后在node环境下（包括bower）
``` bash
npm install
bower install
```
如果本地没有安装mocha、should、karma,需要先全局安装：
``` bash
npm install mocha should karma -g
```
###mocha＋should的node命令行运行：

根目录运行命令：
```bash
mocha 
```
后会直接运行test目录的所有test cases

###mocha＋should的浏览器环境运行：

浏览器打开：test/runner.html

###karma＋mocha＋should的浏览器环境运行：

命令行运行：
```bash
karma start
```
运行后，自动生成coverage reporter在目录“coverage/浏览器目录” 下