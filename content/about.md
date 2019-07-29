---
title: "About"
---
网站是使用`hugo` + `GitHub`搭建的静态网页。在这之前，一直用的是`cnblogs`，自从无意间看到了[https://neojos.com/about/](https://neojos.com/about/),
其实来源是[blog](https://yihui.name/cn/about/),参考[github](https://github.com/rbind/yihui),[github](https://github.com/rstudio/blogdown)。
不得不说，自己被博主的优秀以及博客的简单明了折服的一塌糊涂。

简单了解[hugo](http://gohugo.io/getting-started/)之后，发现它更简单、更方便，所以将自己的博客也做了迁移。

直接git clone git@github.com:GitHubSi/blog.git,然后简单修改里面的内容，删除掉作者的博客，添加自己的博客

使用介绍：
----
1. [Hugo快速入手](https://www.gohugo.org/)
1. [博客模版](https://github.com/GitHubSi/blog)
1. [theme:hugo-ivy](https://github.com/yihui/hugo-ivy)
1. 主题hugo-ivy借鉴[ivy](https://github.com/dmulholl/ivy)：`Ivy is a static website generator built in Python. It's small, elegant, and easy to use.`
1. hugo 编译
2. hugo server 本地启动服务
1. hugo new blog/2019/xxx.md 创建一篇新博客，目录位于content/blog/2019
1. sh simple-deploy.sh 发布博客到xxx.github.io
2. 博客里的图片放在目录hogo_blog/static/img/下

评论系统
----
如果需要，可以考虑[isso](https://posativ.org/isso/)，因为涉及交互，就不再是静态网站了，需要存储系统，就需要放存储系统的服务器。暂时不考虑。
评论系统效果[参考](https://lowentropy.me/flight-rules/20181130-%E4%BD%BF%E7%94%A8pandoc%E5%92%8Ckatex%E4%B8%BAhugo%E6%B7%BB%E5%8A%A0latex%E6%94%AF%E6%8C%81/)

优化方向
----
1. 里面内嵌了很多js，需要删除无用的，把有用的js链接本地化，减少网络依赖

注意事项：
---
1. 修改hugo new xx.md的模版

    >位于archetypes/default.md,参考[.Format](https://gohugo.io/functions/format/)
    
        ---
        title: "{{ replace .Name "-" " " | title }}"
        date: {{ .Date }}
        categories: {{ dateFormat "2006-01" .Date }}   这里必须是"2006-01",即必须是2006年，必须是1月
        tags: []
        author: sgl
        ---


2. 修改样式

    位于static/css/*.css，比如：
    + 博客界面的整体宽度：        
        static/css/style.css
                
            body {
              width: 1360px;
              margin: 0 auto;
              padding: 30px 0;
            }
    + 博客界面右侧边栏的宽度：
        static/css/style.css
    
            .main {
              width: 400px;
              padding: 32px 10px 20px 50px;
              margin-left: 260px;
              border-left: 3px solid black;
              min-height: calc(100vh - 60px);
            }
        
    + 博客界面左侧边栏的宽度：        
        static/css/style.css    
                      
            .masthead {
              width: 60px;
              padding: 20px 50px 20px 10px;
              float: left;
            }
3. hugo配置config.toml含义：https://gohugo.io/getting-started/configuration/
    
    [toml文件](https://github.com/toml-lang/toml)        
