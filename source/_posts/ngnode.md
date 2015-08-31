title: MEAN框架介绍：ngNode
date: 2015-08-17 16:00:00
tags: [node.js, mean]
---

## ngNode
[ngNode](https://github.com/hcnode/ngNode)是一个可以简单快速开发MEAN web应用的framework

## 特点
* 实时的MEAN应用，通过配置生成web api，angularjs构建ui界面.
* 通过简单的配置，简单快速创建web应用，除此之外不需要做其他事情
* 自动生成相关功能，包括：mongodb ODM、CRUD api、数据显示datatable、排序、分页、搜索、编辑数据等
* 内置User模块和[passport.js](https://github.com/jaredhanson/passport)验证模块
<!-- more -->
## MEAN相关:
This library runs under MEAN, you can find some infomation about MEAN below.
* MongoDB - Go through [MongoDB Official Website](http://mongodb.org/) and proceed to their [Official Manual](http://docs.mongodb.org/manual/), which should help you understand NoSQL and MongoDB better.
* Express - The best way to understand express is through its [Official Website](http://expressjs.com/), which has a [Getting Started](http://expressjs.com/starter/installing.html) guide, as well as an [ExpressJS Guide](http://expressjs.com/guide/error-handling.html) guide for general express topics. You can also go through this [StackOverflow Thread](http://stackoverflow.com/questions/8144214/learning-express-for-node-js) for more resources.
* AngularJS - Angular's [Official Website](http://angularjs.org/) is a great starting point. You can also use [Thinkster Popular Guide](http://www.thinkster.io/), and the [Egghead Videos](https://egghead.io/).
* Node.js - Start by going through [Node.js Official Website](http://nodejs.org/) and this [StackOverflow Thread](http://stackoverflow.com/questions/2353818/how-do-i-get-started-with-node-js), which should get you going with the Node.js platform in no time.

## 如何使用
dependency:
```javascript
"ngnode": "2.0.5"
```
install dependency
```bash
npm install
```
创建应用相关配置
```javascript
var config = {
	appName : "testapp",
	mongodConnection : "mongodb://localhost:27017/ngnode",
	on : {
		"someaction" : function (req, res, model, cb) {
			// hook all models here you want when "someaction" execute.
			// and can define in the model config as well
			// see the model config below for detail
		}
	}
};
require("ngNode")(config);
```

保存到app.js然后运行node
```bash
node app.js
```

猜一下发生了什么？你刚才已经创建了一个MEAN应用！很简单，是吧？

## 为什么ngNode可以这么简单
通过前面的配置，我们很轻易的就可以创建一个MEAN应用，并且里面已经包含一个user模块

并且你还可以创建自定义模块，那么当创建自定义模块后，ngNode会自动做些什么事情？

**服务器端**
 1. 通过自定义字段定义，生成mongodb的collection的配置
 2. 自动创建crud的服务
 3. 自动创建crud路由
 4. app运行是调用initData进行数据初始化
 5. 注入自定义hooks

**浏览器端**
 1. 自定义字段的显示title、是否支持排序、是否可以编辑等
 2. 自动生成数据表格显示，并且有分页功能
 3. 自动生成“新建”、“编辑”、“删除”操作链接，并且自动生成ajax调用
 4. 通过field的render配置，可以自定义显示内容
 5. 多种编辑模式，包括普通文本、textarea、日期选择、单选框、列表选择

## 如何创建自定义model
ngNode 会尝试在根目录的“/models”下查找自定义的model

自定义的model配置说明如下：

```javascript
module.exports = function () {
	return {
        name: "blog", // 模块名称，作为mongodb的collection名称
        schema: { // collection的字段配置
            title: { // 字段名
                text: "Title", // 在表格中header的显示
                type: String, // 数据类型
                editType : "textarea" // [optional] 编辑类型，默认是普通文本
                editable : true, // [optional] 是否可以编辑，默认是true
                sortable: true, // [optional] 是否可以排序，默认是false
                render : function(data){ // [optional] 内容显示
                                // data : 字段的值
                    // return 需要显示的内容
                }
            },
            ... other fields
        }
        defSortField: "title", // 默认排序字段
        queryFields: ["title"], // 搜索的字段
        defaultPageSize: 15, // 默认每页数据条数
        on: { // hook配置，只hook当前model，如果需要hook所有model，那么必须在app的配置里面配置on
            "beforeCreate": function (req, res, model, cb) {
                // 创建之前调用
                cb(model); // 如果想继续调用，则回调cb
                cb({err : "如果不想创建，则回调传入有err属性的对象"});
                // 所以当你想继续创建的话，比如避免数据对象出现err属性
            }
        },
        initData : function(mongoose){
            // 初始化调用，app运行时候调用
        }
    }
};
```

## 为什么我只需要创建一个model的配置，就可以在服务器端和浏览器端使用，怎么做到的？
当app启动时，ngNode使用[browserify](http://browserify.org/)打包自定义model，并可以在浏览器的web端使用。

## dependencies in ngNode
 - [express](http://expressjs.com) : ngNode is a framework of MEAN, so express is included
 - [mongoose](http://mongoosejs.com) : my first choice mongodb ORM
 - [passport](http://passportjs.org): I use passport as authentication
 - [express-session](https://github.com/expressjs/session) : use express-session to maintain user authentication
 - [connect-mongo](https://github.com/kcbanner/connect-mongo) : and use mongodb as session store in express-session
 - [browserify](http://browserify.org) : model module used in server side and browser side, browserify definitely
 - [bcrypt](https://github.com/ncb000gt/node.bcrypt.js) : user model use bcrypt to encode password when create user, and compare password when user login


## ngNode 示例
[ngNode-sample](https://github.com/hcnode/ngNode-sample)
 - git clone and run `npm install` and run `npm start`
 - visit in chrome: `http://localhost:9527/user` or `http://localhost:9527/blog` or `http://localhost:9527/yourownmodel` when you add "yourownmodel"
 - login with test@163.com/test



