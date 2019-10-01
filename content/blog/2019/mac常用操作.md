---
title: "Mac常用操作"
date: 2019-05-13T10:43:39+08:00

categories: 2019-05
tags: [mac,]
author: sgl
---

1. 在Finder中显示/隐藏隐藏文件

    defaults write com.apple.Finder AppleShowAllFiles YES/NO  
    killall Finder
2. 打开多个实例
    
    open -n /Applications/xxx.app    
    
3. tcp路由查看
    
    netstat -r
    netstat -ant 1
    
4. 查看端口/进程

    lsof -i:port
    lsof -p pid   
    
5. 数学公式软件

    latex    