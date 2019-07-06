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