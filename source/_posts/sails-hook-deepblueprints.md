title: Sails和blueprints 简介以及deepblueprints hook介绍
date: 2015-10-13 09:20:32
tags: [sails, hook, blueprints]
---

## 关于Sails

[Sails](http://sailsjs.org/) 是一个很优秀的node.js的web服务器框架，提供完整的web服务的解决方案，比如：
 * 丰富的[cli命令](http://sailsjs.org/documentation/reference/command-line-interface)
 * 多种[数据库adapter](http://sailsjs.org/documentation/reference/configuration/sails-config-connections)支持
 * 数据ORM模型[waterline](https://github.com/balderdashy/waterline)
 * [blueprints](http://sailsjs.org/documentation/reference/blueprint-api?q=blueprint-routes)的RESTFul接口实现
 * 权限的[Policies](http://sailsjs.org/documentation/concepts/policies)管理方案
 * 提供方便的[自定义response](http://sailsjs.org/documentation/concepts/custom-responses)方案
 * 文件上传[解决方案](http://sailsjs.org/documentation/concepts/file-uploads)和支持文件上传的[skipper库](https://github.com/balderdashy/skipper)
 * 编写[自定义的hook](http://sailsjs.org/documentation/concepts/extending-sails/hooks)插件
 * 提供RESTFul的Api接口[测试方案](http://sailsjs.org/documentation/concepts/testing)
 * 提供各种Security的[解决方案](http://sailsjs.org/documentation/concepts/security)
    包括：CORS,CSRF,ClickJacking,Hijacking,DDos,P3P,XSS等
<!-- more -->
 
## blueprints
 
blueprints是sails其中一个最出色的功能，blueprints可以瞬间生成完整、复杂的RESTFul接口，包括除了正常的数据增删改查、还包括model之间的associations增删改查，比如有两个model：

```javascript
/**
* Company.js
*/
module.exports = {

  attributes: {
    name : "string",
    team:{
      collection: 'team'
    },
    user:{
      collection: 'user'
    }
  }
};
```

```javascript
/**
* Team.js
*/
module.exports = {

  attributes: {
    name : "string"
  }
};

```

如果创建company下的team，可以直接post team的数据

```javascript
$http.post('/company/1/team', {name:"Android"}).success(function(result){
    result.should.be.have.property("id", 1);
    result.should.be.have.property("team").with.lengthOf(1);
    _.findWhere(result.team, {name:"Android"}).should.be.ok
        .and.have.property("id");
})
```

如果要添加一个已存在的team，可以post team的id，这样不会创建新的team，只是保存team 2和company 1的关系

```javascript
$http.post('/company/1/team', {id:2}).success(function(result){
    result.should.be.have.property("id", 1);
    result.should.be.have.property("team").with.lengthOf(1);
    _.findWhere(result.team, {name:"Android"}).should.be.ok
        .and.have.property("id");
})
```

blueprints的查询数据接口，会自动populate所有associations的attributes

例如：get /company/1的结果

```json
{
    "id" : 1,
    "name" : "netease",
    "team" : [
        {
            "name" : "Web" 
        },{
            "name" : "Android" 
        },{
            "name" : "iOS" 
        }
    ],
    "user" : [
        {
            "name" : "Peter" 
        },{
            "name" : "Harry" 
        }
    ]
}
```

##  sails-hook-deepblueprints

前面介绍了sails的blueprints的一些很好的特点，其实blueprints在sails中是hook插件方式存在的，只不过是内置的hook，默认是enable，有兴趣的可以查看一下[blueprints的源码](https://github.com/balderdashy/sails/tree/master/lib/hooks/blueprints)

如果有超过两个model存在层级关系的associate，比如modelA，modelB，modelC分别存在下一个的attribute，那么blueprints的查询和添加会分别自动生成两个route：

`/modelA/:modelA_id/modelB/:modelB_id?` 
`/modelB/:modelB_id/modelC/:modelC_id?`

大多数情况下，这些api或许也够用，但是如果在某些特殊的情况，比如数据的权限访问，如果我要创建一个modelC，在创建modelC之前，我不等不先通过modelB的id查询到对应的modelA的id，然后确认这个id是否有权限，才创建modelC，否则的话返回一个错误。

这样的话，开发者必须做一些额外的工作去做查询上层数据，但如果一开始我可以知道这个数据对应的modelA的id，那么就可以省掉查询modelB是否属于modelA，我只需要知道当前user是否有权限访问modelA的id，其余事情交给代码去验证。比如：`/modelA/:modelA_id/modelB/:modelB_id/modelC/:modelC_id?` 只需要验证当前用户是否可以访问modelA_id就可以.

sails-hook-deepblueprints就是一个做这个事情的hook，当请求的资源层级超过1层时，就会去验证资源的层级关系是否一一对应，如果不是，将返回一个错误。

### 特点

sails-hook-deepblueprints 是 sails 的[自定义hook](http://sailsjs.org/documentation/concepts/extending-sails/hooks), 提供超过两层的资源层级的route

 * 创建超过2层资源层级的route： `/deep/company/1/team/2/project/3`
 * 验证route的相邻资源的层级是否合法
    比如： 如果team 2不属于company 1，那么这个请求将会返回400错误
    
### 使用方法

 * 将配置放入你所希望生成deep-blueprints的controller里面

    ```javascript
    /**
     * CompanyController
     *
     * @description :: Server-side logic for managing companies
     * @help        :: See http://sailsjs.org/#!/documentation/concepts/Controllers
     */
    
    module.exports = {
      _config : {
        deepBluePrint : true
      }
    };
    ```
 * 启动sails后，将会自动生成deep-blueprints' routes 并使用前缀: /deep   
 
### 例子
 
 [这里](https://github.com/hcnode/deep-blueprints-sample) 有完整的例子
 
 比如这里有4个model，它们分别都是前一个资源是后一个资源的父类
 
```javascript
/**
* Company.js
*/
module.exports = {

  attributes: {
    name : "string",
    team:{
      collection: 'team'
    }
  }
};
```

```javascript
/**
* Team.js
*/
module.exports = {

  attributes: {
    name : "string",
    project : {
      collection: 'project'
    }
  }
};

```


```javascript
/**
* Project.js
*/

module.exports = {

  attributes: {
    name : "string",
    todolist : {
      collection: 'todolist'
    }
  }
};

```

```javascript
/**
* Todolist.js
*/

module.exports = {

  attributes: {
    name : "string"
  }
};
```

 
启动sails后，你会看到自动生成了deep-blueprints的routes（打开sails的log level为silly时可以看到log显示）
 
 ```bash
 silly: Binding RESTful deepblueprint/shadow routes for model+controller: company
 silly: Binding RESTful association deepblueprint `team` for company
 silly: Binding route ::  post /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  post /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/:parentid/team/:id? (ACTION: company/_config)
 silly: Binding RESTful association deepblueprint `project` for team
 silly: Binding route ::  post /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  post /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/*/team/:parentid/project/:id? (ACTION: company/_config)
 silly: Binding RESTful association deepblueprint `todolist` for project
 silly: Binding route ::  post /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  post /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  delete /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  put /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 silly: Binding route ::  get /deep/company/*/team/*/project/:parentid/todolist/:id? (ACTION: company/_config)
 ```
 
 当请求的path命中这些route时，那么deep-blueprints hook将会接管这些请求，并进行对应的数据处理，和blueprints的类似：
 
 ```
 add: post %s/:parentid/%s/:id?
 remove: delete %s/:parentid/%s/:id?
 update: put %s/:parentid/%s/:id?
 populate: get %s/:parentid/%s/:id?
 ```
 
### 注意
 
  * model的关联其他model attribute的名称必须和model名称一模一样，比如:
  
```javascript
/**
* Company.js
*/
module.exports = {

 attributes: {
  name : "string",
  team:{ // team is OK, teams is NOT OK
    collection: 'team'
  }
 }
};
```

```javascript
/**
* Team.js
*/
module.exports = {

 attributes: {
  name : "string"
 }
};
```
  * 因为deep-blueprints 会自动查询所有attributes的associations，并自动创建对应的crud route，所以应该避免循环route，所以**不能定义双向associations**，比如：
 
```javascript
/**
* Company.js
*/
module.exports = {

attributes: {
 name : "string",
 team:{
   collection: 'team'
 }
}
};
```

```javascript
/**
* Team.js
*/
module.exports = {

attributes: {
 name : "string",
 company : {
     model : 'company'
 },
 project : {
   collection: 'project'
 }
}
};

```
 
 ## Test
 in [deep-blueprints sample](https://github.com/hcnode/deep-blueprints-sample), clone repo and run npm test