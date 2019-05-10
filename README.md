# shengulong's blog

Hugo是由Go语言实现的静态网站生成器。它能非常方便的把markdown文件转换为html。
这个仓库包含hugo的源码.md和编译后的的html，直接执行hugo server命令，可以本地打开博客。也可以直接访问线上https://shengulong.github.io/

## 配置submodules

当前项目下·public目录是作为submodules存在的，当使用包管理工具时，需要明确声明.gitmodules文件，内容如下：

```xml
[submodule "public"] 
  path = public 
  url = https://github.com/shengulong/shengulong.github.io.git
```

## 自动提交

这个博客由2部分代码组成：

1. 源码：git@github.com:shengulong/hugo_blog.git
2. `hugo`编译后的代码，这个直接在源码代码中嵌套了`submodules`。位于仓库：git@github.com:shengulong/shengulong.github.io.git

所以每次有新的修改都需要提交两次，而且每次还需要输入用户名和密码，这真是痛苦无比的事情。最终还是提交了`simple-deploy.sh`脚本，来自动做这件事。
执行脚本 sh -x simple-deploy.sh,完成两个仓库的提交

## hugo生成一个新的站点
1. `hugo new site /path/to/site` 生成新的网站
    ```
     Congratulations! Your new Hugo site is created in /path/to/site.
     
     Just a few more steps and you're ready to go:
     
     1. Download a theme into the same-named folder.
        Choose a theme from https://themes.gohugo.io/, or
        create your own with the "hugo new theme <THEMENAME>" command.
     2. Perhaps you want to add some content. You can add single files
        with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
     3. Start the built-in live server via "hugo server".
     
     Visit https://gohugo.io/ for quickstart guide and full documentation.```


2. `hugo new about.md` 写一篇新的文章
3. 安装主题theme
    ```go
    cd themes
    git clone https://github.com/spf13/hyde.git
    ```
4. `hugo server --theme=hyde --buildDrafts` 使用某个主题运行hugo。运行期间具有热加载功能。   

## hugo常用命令

1. hugo 编译代码，在public目录生成新的内容
2. hugo server 启动网站，本地调试
3. hugo new `<SECTIONNAME>/<FILENAME>.<FORMAT>` 新建文件：e.g. hugo new blog/2018/test.md 注意.md后缀格式
4. hugo new site/theme 新建网站/主题
