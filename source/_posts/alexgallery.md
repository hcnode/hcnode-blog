title: AlexGallery：一款基于node.js和nw的图片浏览、预览、批量处理、编辑的开源桌面应用
date: 2015-08-21 10:37:40
tags: [node.js, nw, lwip]
---
![screenshot1](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot1.png)

# 关于AlexGallery


 [AlexGallery](https://github.com/hcnode/AlexGallery)是一款图片浏览、预览、批量处理、编辑的开源图片桌面应用，它是基于node.js环境，使用nw封装，可以在mac、windows等系统使用。

 因为自从我使用Mac后，发现没有一款工具或者软件可以快速浏览和预览图片，虽然系统自带图片预览工具也是可用，但是始终很不方便，我需要快速进入文件夹浏览图片的工具，也用过Google的Picasa，功能很多，但是也还是没有我需要的简单快速浏览方式，其他的比如acdsee又要收费

 所以我决定自己做一个图片预览的工具，而且因为在这之前，我有留意到2个开源库，一个是轻量的图片处理node库[lwip](https://github.com/EyalAr/lwip)，另一个是[nw](https://github.com/nwjs/nw.js)，在做[AlexGallery](https://github.com/hcnode/AlexGallery)之前，我没用过这两个库做过任何东西，但是当我第一次看到[lwip](https://github.com/EyalAr/lwip)的介绍和例子后，我就有个自己写个图片处理工具的想法，两周之前工作忙完差不多就开始动工，因为用了不少开源前端web组件，所以做了一周左右功能基本都出来了
<!-- more -->
# lwip的特点

因为有[lwip](https://github.com/EyalAr/lwip)我才有做这个app的想法，所以这里介绍一下它的特点，也非常感谢[lwip](https://github.com/EyalAr/lwip)和它的作者[Eyal Arubas](https://github.com/EyalAr)

 - 轻量级、简单易用的图片处理库
 - 没有依赖任何第三方库，这意味着build和distribution会简单很多
 - 更重要的是，[lwip](https://github.com/EyalAr/lwip)不是简单的对第三方库比如imagemagick进行封装，而是自己实现所有的功能
 - api使用简单，看一次readme上的示例就只有怎么用了

# AlexGallery的特点
 - 可以快速简单浏览本地文件夹，以树的结构显示在左侧
 - 选择一个含有图片的文件夹后，会自动生成缩略图并显示出来，并提供两个尺寸的缩略图显示方式
 - 可以对当前选择的文件进行图片批量处理，并可以选择缩放，和增加亮度，并且缩放提供三种方式
 - 可以对图片进行幻灯片方式预览
 - 图片处理，可以对图片进行缩放、选择、翻转、效果处理（模糊、锐化、增加亮度等）

# 使用方法
 - 假设你已经有node.js环境并安装了[nw](https://github.com/nwjs/nw.js)和[bower](https://github.com/bower/bower)，如果没有的话，先安装`npm install nw bower -g`
 - git 下载 `git clone https://github.com/hcnode/AlexGallery` 然后 `cd AlexGallery`
 - 安装node依赖库 `npm install`
 - 安装浏览器依赖框架和组件 `bower install`
 - **当安装node依赖库时，node会自动使用node-gyp来build [lwip](https://github.com/EyalAr/lwip)内部c写的库，如果只是在node下使用没有[nw](https://github.com/nwjs/nw.js)封装，是可以正常使用[lwip](https://github.com/EyalAr/lwip)，但是如果在[nw](https://github.com/nwjs/nw.js)下，则需要手动调用nw-gyp手动来build**
    - 先安装 nw-gyp `npm install nw-gyp -g`
	- 进入lwip目录 `cd node_module/lwip`
	- 初始化 build 所需要文件 `nw-gyp configure`
	- build `nw-gyp build`
 - 回到AlexGallery根目录后运行 `nw` 即可运行AlexGallery

# 注意
 - 这个应用暂时只在我的Mac机器上运行过，并且没有任何问题，所以我不清楚在windows下是否有问题，不过理论上[nw](https://github.com/nwjs/nw.js)和node都是跨系统支持的
 - AlexGallery会在使用时候创建$HOME/AlexGallery目录来保存缩略图和编辑时候临时保存的文件，除此之外不会调用你的机器任何东西
 - 首次进入文件夹预览图片时候，AlexGallery会自动生成最大200px尺寸的缩略图，所以第一次浏览的时候需要点时间来生成

# 截图
 ![screenshot1](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot1.png)
 ![screenshot2](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot2.png)
 ![screenshot3](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot3.png)
 ![screenshot4](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot4.png)
 ![screenshot5](https://raw.githubusercontent.com/hcnode/AlexGallery/master/screenshots/screenshot5.png)

# Libraries dependencies
 - [lwip](https://github.com/EyalAr/lwip) Light Weight Image Processor for NodeJS
 - [angular-bootstrap-nav-tree](https://github.com/nickperkinslondon/angular-bootstrap-nav-tree) An AngularJS directive that creates a Tree based on a Bootstrap "nav" list.
 - [ngGallery](https://github.com/jkuri/ngGallery) AngularJS Image Gallery Slideshow
 - [cropper](https://github.com/fengyuanchen/cropper) A simple jQuery image cropping plugin.
 - [seiyria-bootstrap-slider](https://github.com/seiyria/bootstrap-slider) A complete rewrite of the original bootstrap-slider by Stefan Petre.
 - [font-awesome](https://github.com/FortAwesome/Font-Awesome) The iconic font and CSS toolkit




