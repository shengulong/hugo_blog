---
title: "Gerrit分布式实现"
date: 2019-07-06T07:39:37+08:00
categories: 2019-07
tags: [gerrit]
author: sgl
---

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

k8s部署方案
----
google内部源
https://gerrit.googlesource.com/k8s-gerrit/

对应的github源：
https://github.com/GerritCodeReview/k8s-gerrit

插件
----
1. [metrics-reporter-elasticsearch](https://gerrit.googlesource.com/plugins%2Fmetrics-reporter-elasticsearch)
2. [metrics-reporter-jmx](https://gerrit.googlesource.com/plugins%2Fmetrics-reporter-jmx)
3. []()