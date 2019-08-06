---
title: "Jenkins插件开发及调试"
date: 2019-07-25T15:53:17+08:00
categories: 2019-07
tags: [jenkins]
author: sgl
---

### Jenkins插件说明

项目使用mvn hpi:create生成，但是自动生成的示例无法直接使用，对pom.xml文件进行了修改，添加如下内容，指定使用当时最新
的jenkins版本，进行调试
    
    <properties>
        <!--    必须指定这个参数，不然使用插件maven-enforcer-plugin的enforceBytecodeVersion检查时会报错-->
        <java.level>8</java.level>
        <!--    使用最新的jenkins版本进行测试-->
        <jenkins.version>2.176.2</jenkins.version>
    </properties>
    
### 调试方法

执行命令```mvn hpi:run```会自动在项目的根目录下生成work目录，这个目录里有jenkins软件的信息等内容

如图：***在idea上的hpi插件上的run点击debug进入调试模式***,然后在源码或者依赖包的源码上打断点。

![](/img/blog/2019/Jenkins插件开发及调试-0.jpg)


执行成功的话，打开网页http://127.0.0.1:8080/jenkins/就可以看到jenkins的<code>web</code>界面

在idea里修改java文件、jelly文件、html文件，然后就可以在jenkins的web界面及时看的效果    

### 插件的本土化Localization(比如中文化)
插件默认语言是英文，如何本土化？可以参考org.jenkins-ci.main:jenkins-core:2.176.2这类插件的实现。
jenkins本身以及很多插件都是支持多语言的，选择哪种语言，据说jenkins会根据浏览器语言或者计算机系统语言自动选择的。如果选择的语言没有相应的支持，
则显示英文。
1. 安装本土化插件，中文为[Simplified Chinese Plugin](https://github.com/jenkinsci/localization-zh-cn-plugin)
2. 安装的插件自身支持本土化
3. 使用语言切换插件[local](https://plugins.jenkins.io/locale)；配置举例，全局配置里填写："Default Language":zh

插件支持本土化方法：[示例]()
* 帮助文档(添加后缀_zh表示中文文档): help-markupFormatter_zh_TW.html help-markupFormatter_zh.html
* 配置文档(添加后缀_zh表示中文文档): config_zh_TW.properties config_zh.properties 对应于配置文件config.jelly或者config.groovy

插件本土化实现原理：[Localization Support](https://github.com/jenkinsci/localization-support-plugin)

### jenkins中的三种视图

* global.jelly 全局的配置视图
* config.jelly Job的配置视图
* help-属性名.html 帮助视图 html片段

### jelly相关知识
* [Jelly form controls](https://wiki.jenkins.io/display/JENKINS/Jelly+form+controls)
* [core](https://reports.jenkins.io/core-taglib/jelly-taglib-ref.html)
* [jelly:stapler](https://stapler.kohsuke.org/jelly-taglib-ref.html)
* [jelly:core](https://commons.apache.org/proper/commons-jelly/tags.html)
* [Basic guide to Jelly usage in Jenkins](https://wiki.jenkins.io/display/JENKINS/Basic+guide+to+Jelly+usage+in+Jenkins)
* [Forms](https://jenkins.io/doc/developer/forms/)
* [UI Samples Plugin](https://wiki.jenkins.io/display/JENKINS/UI+Samples+Plugin)
* []()

### jenkins框架+插件
jenkins本身不提供很多功能，通过插件来实现各种功能

### jenkins [扩展点](https://jenkins.io/doc/developer/extensions/) [创建一个新的扩展点](https://wiki.jenkins.io/display/JENKINS/Defining+a+new+extension+point)

jenkins有很多的扩展点（ExtensitonPoint），它是Jenkins系统的某个方面的接口或抽象类。这些接口定义了需要实现的方法；
而Jenkins插件需要实现这些方法，也可以叫做在此扩展点之上进行扩展Jenkins


### 示例
github

### 参考
1. https://my.oschina.net/lienson/blog/1531341
2. [jelly](http://commons.apache.org/proper/commons-jelly/index.html)
3. [Understanding Jelly Tags](https://wiki.jenkins.io/display/JENKINS/Understanding+Jelly+Tags)

    https://github.com/jenkinsci/jenkins/tree/master/core/src/main/resources/lib
4.    