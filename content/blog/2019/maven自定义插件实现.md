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

* 插件源码


    /**
     * @goal query
     */
    public class MyQueryMojo
        extends AbstractMojo
    {
        // property做为系统属性使用 -Dquery.url
        @Parameter(property = "query.url", required = true)
        // url为field，对应于pom里的配置url,可以和上面的property不一致
        private String url;
     
        @Parameter(property = "timeout", required = false, defaultValue = "50")
        private int timeout;
     
        @Parameter(property = "options")
        private String[] options;
     
        public void execute()
            throws MojoExecutionException
        {
            ...
        }
    }
    
* 插件配置


    <project>
      ...
      <build>
        <plugins>
          <plugin>
            <artifactId>maven-myquery-plugin</artifactId>
            <version>1.0</version>
            <configuration>
              <!--对应于源码的里的field： url -->
              <url>http://www.foobar.com/query</url>
              <timeout>10</timeout>
              <options>
                <option>one</option>
                <option>two</option>
                <option>three</option>
              </options>
            </configuration>
          </plugin>
        </plugins>
      </build>
      ...
    </project>    

* 通过系统属性system properties定义参数


    mvn myquery:query -Dquery.url=http://maven.apache.org

### 插件代码规范
[Maven Code Style And Code Conventions](https://maven.apache.org/developers/conventions/code.html)

Download [maven-idea-codestyle.xml](https://maven.apache.org/developers/maven-idea-codestyle.xml) and 
copy it to ~/.IntelliJIDEA/config/codestyles then restart IDEA.
After this, restart IDEA and open the settings to select the new code style.