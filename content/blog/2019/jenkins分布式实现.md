---
title: "Jenkins分布式实现"
date: 2019-07-06T07:43:00+08:00
categories: 2019-07
tags: [jenkins]
author: sgl
---

思路
----
查看jenkins[源代码](https://github.com/jenkinsci/jenkins)，把jenkins依赖单机的代码内容，修改为依赖分布式机器的内容；
这样做到应用本身无存储状态，容器化后可以做到随便创建和删除。参阅[jenkins's blog](https://jenkins.io/node/)

jenkins开发相关知识[Extend Jenkins](https://wiki.jenkins.io/display/JENKINS/Extend+Jenkins)、[开发者文档](https://jenkins.io/doc/developer/)
---
1. [jenkins本身开发指导说明](https://github.com/jenkinsci/jenkins/blob/master/CONTRIBUTING.md)
    + java8
    + mavne>=3.5.3
    + OpenJDK
2. [jenkins插件开发指导说明](https://jenkins.io/doc/developer/tutorial/prepare/)
    + [step 1: Preparing for Plugin Development](https://jenkins.io/doc/developer/tutorial/prepare/)
    + [step 2: Create a Plugin](https://jenkins.io/doc/developer/tutorial/create/)
    + [step 3: Build and Run the Plugin](https://jenkins.io/doc/developer/tutorial/run/)
    + [step 4: Extend the Plugin](https://jenkins.io/doc/developer/tutorial/extend/)
    + [Plugin tutorial](https://wiki.jenkins.io/display/JENKINS/Plugin+tutorial)
    + [Plugin Cookbook](https://wiki.jenkins.io/display/JENKINS/Plugin+Cookbook)
    + [Create a new Plugin with a custom build Step](https://wiki.jenkins.io/display/JENKINS/Create+a+new+Plugin+with+a+custom+build+Step)
    + [Plugin Structure](https://wiki.jenkins.io/display/JENKINS/Plugin+Structure)
    + [Making your plugin behave in distributed Jenkins](https://wiki.jenkins.io/display/JENKINS/Making+your+plugin+behave+in+distributed+Jenkins)
    + [IntelliJ IDEA plugin for Stapler方便在idea里开发jenkins的插件](https://wiki.jenkins.io/display/JENKINS/IntelliJ+IDEA+plugin+for+Stapler)
    + [Exposing data to the remote API](https://wiki.jenkins.io/display/JENKINS/Exposing+data+to+the+remote+API)
    + [Manage global settings and tools installations](https://wiki.jenkins.io/display/JENKINS/Manage+global+settings+and+tools+installations)
    + [Debugging native Maven jobs](https://wiki.jenkins.io/display/JENKINS/Debugging+native+Maven+jobs)
    + [Remote debugging a Java application](https://stackoverflow.com/questions/975271/remote-debugging-a-java-application)
3. 快速打包jenkins.war,The WAR file will be created in war/target/jenkins.war

4. [jenkins启动](https://wiki.jenkins.io/display/JENKINS/Starting+and+Accessing+Jenkins)
5. [intellij idea远程调试jenkins](http://blog.nsfocus.net/intellij-idea-remote-debugging-jenkins/)
6. [Becoming a Jenkins contributor: Newbie-friendly tickets](https://jenkins.io/blog/2019/05/30/becoming-contributor-newbie-tickets/)
7. [docker打包jenkins](https://jenkins.io/zh/blog/2018/10/16/custom-war-packager/)
    
    mvn clean package -pl war -am -DskipTests -Dfindbugs.skip
目前来看主要涉及jobs/workspace内容等

jenkins相关知识点
---
1. Blue Ocean重新定义jenkins的用户体验，侧重pipeline：https://jenkins.io/zh/doc/book/blueocean/
2. 把jenkins的slave/agent的内容存储到其他存储系统，从而降低master和slave的通信压力：https://jenkins.io/zh/blog/2018/09/10/scaling-network-connections/
3. jenkins的k8s使用：https://jenkins.io/zh/blog/2018/09/14/kubernetes-and-secret-agents/ 
4. jenkins的python支持：https://jenkins.io/zh/solutions/python/
5. hudson和jenkins的区别就是mysql和mariadb的区别，前者被oracle收购后，开发团队就开发了后者
6. [jenkins api介绍](https://wiki.jenkins.io/display/JENKINS/Remote+access+API#space-menu-link-content)
    里面介绍了推荐的python和java封装好的api；同时jenkins信息的获取是通过xpath获取的,比如通过http://127.0.0.1:8080/api/json?depth=1
    或者http://127.0.0.1:8080/api/xml?depth=1可以获取一些较少的关键信息,通过http://127.0.0.1:8080/api/json?tree=jobs[name]{0,12}
    或者http://127.0.0.1:8080/api/xml?tree=jobs[name]{0,12}可以获取前12个job的信息(这里tree=jobs[name]使用了xpath过滤)；每项任务的操作，在web界面上可以
    看到链接；授权采用的是基本授权(Basic Auth)
7. [Jenkins中文社区](https://jenkins-zh.cn)    

分布式部署实现
-----
1. [关于Jenkins master共享JENKINS_HOME目录的实验](https://jenkins-zh.cn/wechat/articles/2019/04/2019-04-23-jenkins-master-shared-home/)验证了当前的jenkins在
分布式部署时的问题:通过共享强依赖的单机目录(k8s上实现多个容器挂载相同的目录,可以借助ceph分布式存储)，无法实现
2. 针对上面实验出现的问题，阅读jenkins[源代码](https://github.com/jenkinsci/jenkins)，看是否可以通过修改Jenkins应用的源代码解决
3. 首先解决job相关的分布式实现，其次解决插件相关的分布式实现，还有Jenkins配置相关的分布式，或许通过配置本地化存储修改为远端数据库存储解决分布式问题
4. 


疑问
===
- [ ] jenkins是不是把job等信息全部存入***内存***？因为jenkins不使用数据库，内容全部落磁盘，把磁盘的部分或者全部信息落入内存，无疑可以加快速度

调试jenkins源代码
====
1. 根据源代码生成jenkins.war包
2. debug模式启动jenkins

        java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=8000 -jar war/target/jenkins.war --httpPort=8081

3. 使用idea开启远程调试
        
        1. 第一句日志目前不知道从哪儿打印的？
        Listening for transport dt_socket at address: 8000
        2. 接下来的日志是 [org.jenkins-ci:executable-war:1.45](https://github.com/jenkinsci/extras-executable-war) 的 `Main.java` 的 `main` 函数输出的，既可以认为这是程序的入口
        3. 接着会调用org.jenkins-ci:winstone:5.3到Launcher.java启动jetty服务
        4. 接着调用jenkins/core/src/main/java/hudson/WebAppMain.java，主要是Creates the sole instance of {@link jenkins.model.Jenkins} and register it to the {@link ServletContext}.
        5. 

删除job的步骤
====
1. 先删除job下的config.xml文件
2. 在递归删除其他文件和目录        

实验结果
===
- [x] job的日志是每次从磁盘加载的，不存内存,根据project名字，去磁盘里检索
    
        检索路径格式：项目名字/builds/构建号/log
        举例：demo/builds/1/log
    
- [x] job的日志是每次从磁盘加载的，不存内存


参与jenkins建设
-----
注册jenkins的wiki和jira账号，为同一个账号
https://wiki.jenkins.io/#all-updates
https://issues.jenkins-ci.org/browse/JENKINS-58948?filter=-4