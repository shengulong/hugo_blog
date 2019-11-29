---
title: "Gerrit分布式实现"
date: 2019-07-06T07:39:37+08:00
categories: 2019-07
tags: [gerrit]
author: sgl
---

> gerrit不支持jre1.8以上版本

思路
====
查看gerrit源代码，把gerrit依赖单机的代码内容，修改为依赖分布式机器的内容；
这样做到应用本身无存储状态，容器化后可以做到随便创建和删除。

CodeReview相关工具：https://github.com/GerritCodeReview

gerrit版本变更：https://www.gerritcodereview.com/releases-readme.html

gerrit源码：

https://gerrit.googlesource.com/gerrit/

https://github.com/GerritCodeReview/gerrit

构建工具：google内部使用的 bazel
bazel安装：https://docs.bazel.build/versions/master/install-os-x.html#install-on-mac-os-x-homebrew
bazel介绍：https://www.cnblogs.com/Leo_wl/p/4458115.html

### [官网](https://www.gerritcodereview.com/)
内容：
+ 插件链接
+ 下载地址
+ 在线ppt，解释gerrit的理念
+ [线文档](https://gerrit-review.googlesource.com/Documentation/index.html)

### build gerrit source code

[Gerrit Code Review - Building with Bazel](http://127.0.0.1:8080/Documentation/dev-bazel.html#installation)
[Gerrit Code Review - IntelliJ IDEA Setup](http://127.0.0.1:8080/Documentation/dev-intellij.html)

    git clone --recursive https://gerrit.googlesource.com/gerrit
    cd gerrit && bazel build release
    上面执行成功的话，会在gerrit目录生成如下war包
    bazel-bin/release.war
    
    cd gerrit && bazel build gerrit
    上面执行成功的话，会在gerrit目录生成如下war包
    bazel-bin/gerrit.war
    
    [切换分支](http://127.0.0.1:8080/Documentation/dev-readme.html#_switching_between_branches)要把子模块一起切换
    git checkout --recurse-submodules
    
[本地启动一个gerrit server](http://127.0.0.1:8080/Documentation/dev-readme.html#_local_server)


### [debug the Gerrit server](http://127.0.0.1:8080/Documentation/dev-readme.html#run_daemon)  

    -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005
  
    
### gerrit初始化配置

http://127.0.0.1:8080/Documentation/config-gerrit.html#_file_etcgerrit_config    
    
### 安装

On CentOS/RedHat run:

    yum clean all && yum install gerrit-<version>[-<release>]


### [gerrit前端开发](/blog/2019/11/10/gerrit前端开发/)


k8s部署方案
----
google内部源
https://gerrit.googlesource.com/k8s-gerrit/

对应的github源：
https://github.com/GerritCodeReview/k8s-gerrit

gerrit的docker镜像
----
https://hub.docker.com/r/gerritforge/gerrit-centos7/tags

To run a CentOS 7 based Gerrit image:

    docker run -p 8080:8080 gerritforge/gerrit-centos7[:version]
    
[来自](https://gerrit.googlesource.com/gerrit)    

### 使用idea+bazel开发

在idea里安装bazel插件，[github](https://ij.bazel.build/)，[插件文档](https://ij.bazel.build/)

bazel插件也是bazel构建的，详见[github](https://ij.bazel.build/)

### 在线试用
https://gerrit-review.googlesource.com/dashboard/self

插件
----
### [打包方法](https://gerrit-review.googlesource.com/Documentation/dev-build-plugins.html)
> ***注意***:
> 1. 一定要开代理
> 2. 保持插件和gerrit的版本一致性，避免版本不兼容

    The fact that plugin contains BUILD file doesn’t mean that building this plugin from the plugin directory works.
    
    Bazel in tree driven means it can only be built from within Gerrit tree. Clone or link the plugin into gerrit/plugins directory:
    
    cd gerrit
    bazel build plugins/<plugin-name>:<plugin-name>
    The output can be normally found in the following directory:
    
    bazel-bin/plugins/<plugin-name>/<plugin-name>.jar
    Some plugins describe their build process in src/main/resources/Documentation/build.md file. It may worth checking.
    
    
    首先配置
    Open YOUR_GERRIT_DIR/etc/gerrit.config file and add the following entry:
        
        [plugins]
            allowRemoteAdmin = true
    然后远程安装插件
    ssh -p 29418 admin@172.19.16.64 gerrit plugin install -n replication.jar - <~/temp/replication.jar
    远程重载/删除/使能插件
    ssh -p 29418 admin@172.19.16.64 gerrit plugin reload/rm/enable replication



> [插件文档](https://gerrit-documentation.storage.googleapis.com/Documentation/3.0.3/config-plugins.html)
> [各类插件](https://gerrit.googlesource.com/plugins/)
> [gerrit && plugins ci](https://gerrit-ci.gerritforge.com/)

1. [metrics-reporter-elasticsearch](https://gerrit.googlesource.com/plugins%2Fmetrics-reporter-elasticsearch)
2. [metrics-reporter-jmx](https://gerrit.googlesource.com/plugins%2Fmetrics-reporter-jmx)
3. []()