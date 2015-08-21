title: Android高效开发实践之－消灭重复的体力活
date: 2015-08-21 14:58:55
tags: android
---

android开发的同学，相信都遇到过在写代码的过程中，需要发费很多时间去做一些重复的劳动，比如：

 - activity和fragment创建后要写一大堆的findViewById来初始化各个view
 - 比如创建一个List的界面，我需要创建相关的一大堆文件包括：
    - activity
    - fragment
    - adapter
    - listener
    - activity layout
    - fragment layout
    - list item layout
    - 数据层相关的比如我们是用retrofit就包括：manager、service、request、response四个类文件
    如果上面这些文件一个个创建，累死之余build时候发现各种缺漏

 - 调试时候每次build一两分钟，上个洗手间回来，发现gradle任务还在运行中
 - 调试webview时候，只能chrome来模拟，调试好了，真机上又有问题
 - 调试app的数据http请求各种繁琐，又加代理连到pc，又要开charles，手机又要连内网，手机的wifi代理经常要反复切换，坑爹的居然没有自动保存代理功能

# 依赖注入DI(Dependency Injection)
前不久，我所负责的项目android版本需要新增一个新的模块，新的模块流程复杂、交互比较多，开工之前预计至少要3周时间开发，
我决定尝试使用一个DI库 Butterknife来开发，配合android studio插件使用，自从用起来后，写代码的心情就一个字：很爽！最终一周多时间基本功能都写出来。
一些常见的android DI库包括

 － Android Annotation
 － Dagger
 － RoboGuice
 － ButterKnife

上面DI库用起来都是大同小异，而且性能都很好，因为它们都是再编译时候生成，而不是运行时反射来生成
Android Annotation看上去功能最强大，支持大量的注解，除了view，event，还包括各种你可以想到的注入，比如service、resource、多线程、甚至还包括http服务注入
看下面官网的例子截图，使用Android Annotation后，代码简洁得令人发指！！！
![androidannotation](/images/androidannotation.jpg)
不过最终我们还是选择了ButterKnife，因为它提供了IDE插件，其实我们的android项目之前也有一套自己写的简单的ViewInject的注解，但是没有插件支持，所以插件支持很重要。
下面gif是官网提供的
![butterknife](http://plugins.jetbrains.com/files/7369/screenshot_14384.png)
另外插件还可以配置生成view的私有变量的前缀，比如我们按照官方规范，统一前面加“m”前缀
![butterknife_plugin](/images/butterknife)
