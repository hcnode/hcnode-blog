title: node-gitignore and commander
date: 2015-08-29 10:48:39
tags: [node.js, commander]
---

前不久写了一个[小工具](https://github.com/hcnode/node-gitignore)实现自定义我自己的.gitignore文件，因为在官方的[.gitignore](https://raw.githubusercontent.com/github/gitignore/master/Node.gitignore)，我不明白为什么没有`bower_components`，因为很多node.js项目可能是会用到浏览器端的依赖。

所以我在自己的工具里面加上了`bower_components`和我的IDE webstorm自动生成的`.idea`。

这样我每次新建node项目，我直接在项目文件夹里面命令行运行`node-gitignore`，就会自动生成我需要的.gitignore文件。

这个工具非常简单，用了[tj](https://github.com/tj)大神的[commander](https://github.com/tj/commander.js)库，可以很方便写node的cli库，commander非常强大、简单、易用，我的这个工具就这么几行代码而已：

```javascript
#!/usr/bin/env node

var program = require('commander');
var request = require('request');
var fs = require('fs');

program
  .version('0.0.1')
  .parse(process.argv);
var append = "# .idea\n.idea\n# Dependency directory\nbower_components";
var ws = fs.createWriteStream('.gitignore');
ws.on("finish", function(){
	fs.appendFile(".gitignore", append, function (err, data) {
		if(err){
			console.log("error.");
		}else {
			console.log("done.");
		}
	});
});
request
  .get('https://raw.githubusercontent.com/github/gitignore/master/Node.gitignore')
  .on('error', function(err) {
    console.log(err)
  })
  .pipe(ws);

```
**使用方法**

```bash
$ npm install node-gitignore -g
$ node-gitignore
```
或者你也可以定制自己的.gitignore文件，你可以fork然后修改代码后，发布到npm上，然后安装后就可以用。