---
title: "Maven插件调试方法"
date: 2019-07-23T22:49:58+08:00
categories: 2019-07
tags: [maven]
author: sgl
---

方法
---

一般的项目，使用IntelliJ IDEA进入Debug模式，通过断点即可进行调试。

但maven编写的插件就不同了，插件需要打包成Jar并被加载到使用它的项目中，执行项目，调用插件，才可以看的插件的运行效果。
如果我们需要在编写插件源码时调试的话，该如何操作？此时便不能直接点击调试按钮进行调试（因为没有Main类）

下面以idea调试为例说明：
##### 调试端配置:以示例插件```hello-maven-plugin```为例
主要代码：需要引入如下所示的三个maven插件，maven引入包然后编写入口类继承AbstractMojo，相当于main函数，
使用注解@Mojo定义生命周期和插件执行名称。我们需要实现execute方法，当用户执行maven运行命令后，这个地方的代码会被自动执行。
    
    
    package sample.plugin;
        
    import org.apache.maven.plugin.AbstractMojo;
    import org.apache.maven.plugin.MojoExecutionException;
    import org.apache.maven.plugins.annotations.Mojo;
    
    /**
     * Says "Hi" to the user.
     *
     */
    // When processing the source tree to find mojos, [plugin-tools](http://maven.apache.org/plugin-tools/)
    // looks for classes with either @Mojo Java 5 annotation or "goal" javadoc annotation.
    // Any class with this annotation are included in the plugin configuration file.
    @Mojo( name = "sayhi")
    public class MyMojo extends AbstractMojo
    {
        public void execute() throws MojoExecutionException
        {
            // 可以在此处打断点，这样调用这个插件时，就会停在这个断点处
            getLog().info( "Hello, world" );
        }
    }  

idea配置：

* 使用```Remote```，```Debugger mode```选择```Attach to remote JVM```，表示此为调试端，用来调试远端的代码；
* 输入被调试端的Host和Port(服务端监听的端口)；
* 选择不同的JDK，下面会显示不同的内容，这段内容(-agentlib...)是给被调试端使用的命令行参数
* ```Use module classpath```选择被调试的源码
* 执行Debug按钮不是Run按钮


![我是个图片](/img/blog/2019/Maven插件调试方法-0.jpg)

##### 被调试端配置:以插件```maven-dependency-plugin```为例
其pom.xml里配置如下，在这个插件根目录下执行mvn validate就会调用插件```hello-maven-plugin```的sayhi

    <build>
        <plugins>
          <plugin>
            <groupId>sample.plugin</groupId>
            <artifactId>hello-maven-plugin</artifactId>
            <version>1.0.0</version>
            <executions>
              <execution>
                <id>test</id>
                <phase>validate</phase>
                <goals>
                  <goal>sayhi</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
        </plugins>
      </build>

idea配置：
* 在Parameters选择插件```maven-dependency-plugin```的工作目录，命令行为validate
* 在Runner去掉选项```Use project settings```，把调试端产生的命令行参数粘贴到```VM Options```里
* 执行Run按钮不是Debug按钮

![](/img/blog/2019/Maven插件调试方法-1.jpg)

![](/img/blog/2019/Maven插件调试方法-2.jpg)

#### 被调试端更简单的方法：
在项目根目录直接执行```mvnDebug sample.plugin:hello-maven-plugin:1.0.0:sayhi``` 

查看mvnDebug的内容，可见也是配置的相同或者类似的调试项(不同的JDK，调试配置不同)

    #!/bin/sh   
    # -----------------------------------------------------------------------------
    # Apache Maven Debug Script
    #
    # Environment Variable Prerequisites
    #
    #   JAVA_HOME       Must point at your Java Development Kit installation.
    #   MAVEN_OPTS      (Optional) Java runtime options used when Maven is executed.
    #   MAVEN_SKIP_RC   (Optional) Flag to disable loading of mavenrc files.
    # -----------------------------------------------------------------------------
    
    MAVEN_DEBUG_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=8000"
    echo Preparing to execute Maven in debug mode
    env MAVEN_OPTS="$MAVEN_OPTS" MAVEN_DEBUG_OPTS="$MAVEN_DEBUG_OPTS" "`dirname "$0"`/mvn" "$@"

java调试项配置说明
-----
执行命令```java -agentlib:jdwp=help```

    Java Debugger JDWP Agent Library
                   --------------------------------
    
      (see http://java.sun.com/products/jpda for more information)
    
    jdwp usage: java -agentlib:jdwp=[help]|[<option>=<value>, ...]
    
    Option Name and Value            Description                       Default
    ---------------------            -----------                       -------
    suspend=y|n                      wait on startup?                  y
    transport=<name>                 transport spec                    none
    address=<listen/attach address>  transport spec                    ""
    server=y|n                       listen for debugger?              n
    launch=<command line>            run debugger on event             none
    onthrow=<exception name>         debug on throw                    none
    onuncaught=y|n                   debug on any uncaught?            n
    timeout=<timeout value>          for listen/attach in milliseconds n
    mutf8=y|n                        output modified utf-8             n
    quiet=y|n                        control over terminal messages    n
    
    Obsolete Options
    ----------------
    strict=y|n
    stdalloc=y|n
    
    Examples
    --------
      - Using sockets connect to a debugger at a specific address:
        java -agentlib:jdwp=transport=dt_socket,address=localhost:8000 ...
      - Using sockets listen for a debugger to attach:
        java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y ...
        
        java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5503 -jar  xxxx.jar
        使用中发现选项-jar要放在选项-agentlib后面
       
    Notes
    -----
      - A timeout value of 0 (the default) is no timeout.
    
    Warnings
    --------
      - The older -Xrunjdwp interface can still be used, but will be removed in
        a future release, for example:
            java -Xdebug -Xrunjdwp:[help]|[<option>=<value>, ...]

启动调试协议jdwp，全称是Java Debug Wire Protocol，它定义了JPDA front-end和JPDA back-end之间通讯信息的二进制格式。
这里的通讯信息主要包括两种：调试器发送给JVM的请求信息和JVM发送给调试器的调试信息
* server:该jvm是调试者(调试器)还是被调试者
* suspend:是否等待外部调试器的连接，如jetty启动时候，是否等待idea的远程连接后在进行jetty的初始化工作。在调试web容器的时候用的很多
如果设成 y, 则应用将暂停不运行，直到有调试者连接上
* transport:JPDA front-end和back-end之间的传输方法。dt_socket表示使用套接字传输。
* address:监听的地址和端口

原理
----
一切源于被称作 Agents 的东西。

运行着各种编译过的 .class 文件的JVM，有一种特性，可以允许外部的库（Java或C++写的libraries）在运行时注入到 JVM 中。
这些外部的库就称作 Agents, 他们有能力修改运行中 .class 文件的内容。
这些 Agents 拥有的这些 JVM 的功能权限，是在 JVM 内运行的 Java Code 所无法获取的， 他们能用来做一些有趣的事情，比如修改运行中的源码，性能分析等。 
像 JRebel 工具就是用了这些功能达到魔术般的效果。

传递一个 Agent Lib 给 JVM, 通过添加 agentlib:libname[=options] 格式的启动参数即可办到。像上面的远程调试我们用的就是 **-agentlib:jdwp=... **来引入 jdwp 这个 Agent 的。
jdwp 是一个 JVM 特定的 JDWP（Java Debug Wire Protocol） 可选实现，用来定义调试者与运行JVM之间的通讯，它的是通过 JVM 本地库的 jdwp.so 或者 jdwp.dll 支持实现的。

简单来说，jdwp agent 会建立运行应用的 JVM 和调试者（本地或者远程）之间的桥梁。既然他是一个Agent Library, 它就有能力拦截运行的代码。
在 JVM 架构里，debugging 功能在 JVM 本身的内部是找不到的，它是一种抽象到外部工具的方式（也称作调试者 debugger）。这些调试工具或者运行在 JVM 的本地 或者在远程。这是一种解耦，模块化的架构。


还有一种场景， 你得到了一个可以部署的war/jar包，只有class没有java源代码，而应用部署在本地/远程后，是否可以调试? 读完本文就明白怎么做了。

注意
---
1. 源码调试时，只能观察执行过程情况，并不能改变原来的代码行为
2. 仓库里首先得存在这个依赖，不然使用这个依赖的被调试者就会报依赖找不到的错误；然后存在相同的源码，这样就可以调试源码里
3. 调试a项目的源码时，b项目使用了a项目，并触发了a项目的断点，断点才有效果

参考
----
1. https://www.jianshu.com/p/d168ecdce022
2. https://blog.trifork.com/2014/07/14/how-to-remotely-debug-application-running-on-tomcat-from-within-intellij-idea/
3. https://docs.oracle.com/javase/7/docs/technotes/guides/jpda/architecture.html