---
title: "Maven自定义插件实现"
date: 2019-07-06T07:49:23+08:00
categories: 2019-07
tags: [maven]
author: sgl
---

[maven开发](http://maven.apache.org/)
---

### [maven开发](http://maven.apache.org/developers/index.html)
### [maven插件开发](http://maven.apache.org/plugin-developers/index.html)
1. 插件和生命周期的某个阶段绑定
2. 自定义插件命名为：<yourplugin>-maven-plugin；maven-<yourplugin>-plugin是官方插件的命名，拥有商标权
3. 

### 插件分类
* build插件：会在build阶段被执行，应该配置在POM的<build/>元素中
* reporting插件：生成站点的时候会执行，应该配置在POM的<reporting/>元素中。因为reporting插件的结果是生成的站点的一部分，
所以这种插件应该是国际化和本地化的

### 插件配置
Maven插件都是通过指定一个<configuration>元素来配置的。而该元素中的子元素，都是Mojo中的property。
详见[Guide to Configuring Plug-ins](http://maven.apache.org/guides/mini/guide-configuring-plugins.html)