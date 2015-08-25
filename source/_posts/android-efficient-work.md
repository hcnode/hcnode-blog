title: Android高效开发实践之－消灭重复的体力活
date: 2015-08-21 14:58:55
tags: android
---

android开发的同学，相信都遇到过在写代码的过程中，需要花费很多时间去做一些重复的劳动，比如：

 - activity和fragment创建后要写一大堆的findViewById来初始化各个view
 - 创建一个ListActivity的界面，我需要配套创建相关的一大堆文件包括：
    - activity、fragment、adapter、listener等class
    - activity layout、fragment layout、list item layout等layout
    - Model层相关的比如我们是用retrofit就包括：manager、service、request、response四个类文件
    - 如果上面这些文件一个个创建，累死之余build时候发现各种缺漏
 - 调试时候每次build一两分钟，上个洗手间回来，发现gradle任务还在运行中
 - 调试webview时候，只能chrome来模拟，调试好了，真机上又有问题
 - app的数据http请求调试各种繁琐，又加代理连到pc，又要开charles，手机又要连内网，手机的wifi代理经常要反复切换，坑爹的居然没有自动保存代理功能

上面这些提到的“体力活”，可能会占据了我们开发不少的时间，一方面浪费时间、另一方面无法专心开发具体业务需求，我下面总结了一下消灭这些体力活的方法
<!-- more -->
### 依赖注入DI(Dependency Injection)
估计大家对DI应该有所了解，因为在android开发里面，这是最浪费体力活的工作，前不久，我所负责的项目android版本需要新增一个新的模块，新的模块流程复杂、交互比较多，开工之前预计至少要3周时间开发，我决定尝试使用一个DI库 Butterknife来开发，配合android studio插件使用，自从用起来后，写代码的心情就一个字：很爽！最终整个模块提前一周时间基本功能都写出来。
一些常见的android DI库包括

 - Android Annotation
 - Dagger
 - RoboGuice
 - ButterKnife

上面DI库用起来都是大同小异，而且性能都很好，因为它们基本都是编译时生成一些缓存来提供运行时使用，而不是运行时反射来调用。
Android Annotation看上去功能最强大，支持大量的注解，除了view，event，还包括各种你可以想到的注入，比如service、resource、多线程、甚至还包括http服务注入！
看下面官网的例子截图，使用Android Annotation后，代码简洁得异常残暴！！！
![androidannotation](https://github.com/hcnode/hcnode-blog/blob/master/images/androidannotation?raw=true)
不过最终我们还是选择了ButterKnife，它比较轻量级，如果直接就上AA，担心步子迈得太大，大家不(rong)太(yi)习(che)惯(dan)，不过其实最主要原因还是因为Butterknife提供了IDE插件，其实我们的android项目之前也有一套自己写的简单的ViewInject的注解，但是没有插件支持，所以插件支持很重要，它能真正帮助我们解放体力活。
下面gif是官网提供的介绍动画
![butterknife](http://plugins.jetbrains.com/files/7369/screenshot_14384.png)
另外插件还可以配置生成view的私有变量的前缀，比如统一前面加“m”前缀
![butterknife_plugin](https://github.com/hcnode/hcnode-blog/blob/master/images/butterknife.png?raw=true)

### 批量生成通用模块的模版
我前面一开始所提到的创建一个列表时候，所需要创建一大堆文件，而且这些文件零零散散分布在各个文件夹，这也是一件很繁琐的体力活。
我使用了一个完全没有技术含量的方法来做这些“体力活”，我在项目里面创建了一个工具，然后通过自定义模块来生成需要的文件

 - **首先，创建模版文件**
 我新建立了一个module叫“templateutil”，里面包括需要创建的模版template目录，可以将通用的activity、fragment、layout放在里面
 ![template_folder](https://raw.githubusercontent.com/hcnode/hcnode-blog/master/images/template_folders.png)

 - **创建了两个给gradle执行任务的class**
 分别是CreateListActivity用来创建相关一整套的class和layout、和CreateModel用来创建model层相关class

```java
public class CreateListActivity {
    public static void main(String[] args) {
        System.out.println("===== Create listactivity start =====");
        String model = args[0];
        System.out.println("model:" + args[0]);
        // 获取当前module path
        String currProjectPath = System.getProperty("user.dir");
        // 模版path
        String tmpPath = currProjectPath + "/template/listactivity";
        // app src path
        String appPath = currProjectPath + "/../app/src/main/java/com/netease/mail/oneduobaohydrid/";
        // resource path
        String resPath = currProjectPath + "/../app/src/main/res/layout/";
        /** 各个文件路径定义开始 **/
        String tmpActivityFile = tmpPath + "/Activity.java";
        String activityFile = appPath + "activity/" + Util.upperCaseModel(model) + "Activity.java";
        String tmpAdapterFile = tmpPath + "/Adapter.java";
        String adapterFile = appPath + "adapter/" + Util.upperCaseModel(model) + "Adapter.java";
        String tmpFragmentFile = tmpPath + "/Fragment.java";
        String fragmentFile = appPath + "fragment/" + Util.upperCaseModel(model) + "Fragment.java";
        String tmpActivityXml = tmpPath + "/activity.xml";
        String activityXml = resPath + "activity_" + model + ".xml";
        String tmpFragmentXml = tmpPath + "/fragment.xml";
        String fragmentXml = resPath + "fragment_" + model + ".xml";
        String tmpItemXml = tmpPath + "/layout_item.xml";
        String itemXml = resPath + "layout_item_" + model + ".xml";
        /** 各个文件路径定义结束 **/
        System.out.println("tmpPath:" + tmpPath);
        System.out.println("appPath:" + appPath);
        /** 下面开始读取、替换、保存到目标 **/
        Util.buildFile(tmpActivityFile, activityFile, model);
        Util.buildFile(tmpAdapterFile, adapterFile, model);
        Util.buildFile(tmpFragmentFile, fragmentFile, model);
        Util.buildFile(tmpActivityXml, activityXml, model);
        Util.buildFile(tmpFragmentXml, fragmentXml, model);
        Util.buildFile(tmpItemXml, itemXml, model);
        System.out.println("===== Create listactivity finish =====");
    }
}
```

```java
public class CreateModel {
    public static void main(String[] args) {
        System.out.println("===== Create model start =====");
        String model = args[0];
        System.out.println("model:" + args[0]);
        String currProjectPath = System.getProperty("user.dir");
        String tmpPath = currProjectPath + "/template/model";
        String modelPath = currProjectPath + "/../model/src/main/java/com/netease/mail/oneduobaohydrid/model/" + model.toLowerCase();
        System.out.println("tmpPath:" + tmpPath);
        System.out.println("modelPath:" + modelPath);
        File file =new File(modelPath);
        if  (!file.exists()  && !file.isDirectory()){
            file.mkdir();
            Util.buildFiles(tmpPath, model, modelPath);
        } else {
            System.out.println("!!!!!Model目录已存在" + modelPath + "");
        }
        System.out.println("===== Create model finish =====");
    }
}
```
 - **在gradle配置里面，增加两个task**
两个task分别创建ListActivity任务和创建Model任务

```groovy
// gradle createModel -Pmodel=wishlist
task createModel (type: JavaExec, dependsOn: classes) {
    if(project.hasProperty('model')){
        args(model)
    }
    main = 'com.templateutil.CreateModel'
    classpath = sourceSets.main.runtimeClasspath
}
// gradle createListActivity -Pmodel=wishlist
task createListActivity (type: JavaExec, dependsOn: classes) {
    if(project.hasProperty('model')){
        args(model)
    }
    main = 'com.templateutil.CreateListActivity'
    classpath = sourceSets.main.runtimeClasspath
}
```

 - **然后就可以使用了**
```bash
harry$ gradle createListActivity -Pmodel=wishlist
```

### 缩短gradle build task的时间

现在一些大型的android项目，里面可能存在多个module，依赖很多库，每次需要run或者debug时候，需要一两分钟时间等待task run完，才能调试，下面有三个方法缩短这个build时间
#### 修改gradle配置，
爆栈里的这个[修改gradle配置方法](http://stackoverflow.com/questions/16775197/building-and-running-app-via-gradle-and-android-studio-is-slower-than-via-eclips)经过实践证明确实可以减少一点build时间，在我的项目里面大概可以减少10-20s
#### [buck](https://github.com/facebook/buck)
来自facebook的buck，据说可以将build时间缩短到十分之一，WOW！不过我没有用过，因为看介绍说，它放弃了gradle的build system，并且需要修改大量的代码，所以我们放弃用它
#### [LayoutCast](https://github.com/mmin18/LayoutCast)
当看到LayoutCast README的第一行说明，我忍不住想跟作者握个抓

>Android SDK sucks. It's so slow to build and run which waste me a lot of time every day.

LayoutCast最大的好处是，不需要修改太多的代码，速度方面在介绍中有数据对比，据称比buck还要快，我试过放入我们的项目，可能是因为我们的项目结果比较复杂，一直没有运行成功，不过我新建project测试是可以用的

### webview调试插件
有个很好用的调试真机webview的[chrome插件](https://chrome.google.com/webstore/detail/adb/dpngiggdglpdnjdoaefidgiigpemgage)，通过adb连接直接调试真机，而不需要chrome模拟移动设备
![adb_plugin](https://lh6.googleusercontent.com/UWMstNihpq4VCeaTA4nkWMpnjZ_RlZ0SJa4rPh9Rc63nPT6r8nJ6Ju03BYfm7LTwtH7L5VMy=s1280-h800-e365-rw)

### [AndroidProxySetter](https://github.com/jpkrause/AndroidProxySetter)
最后这个是一个小工具，使用adb命令可以快速进行wifi代理的设置和清除，亲测有效！