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
    
6. 接力(handoff)  
    
    > 在一个设备上复制的内容，可以在另一个设备上粘贴；
    > 在一个设备上浏览的内容，可以在另一个设备上接着浏览
    
        两个设备属于同一个AppleID/开启蓝牙和接力功能/处于同一个网络
       

7. 截屏

        cmd+shift+3：对整个屏幕进行截图
        cmd+shift+4：对自行选择的区域进行截图
        cmd+shift+4+space（空格键）对选定的某个应用程序界面窗口进行截图
        Xnip/Snip滚动截图，实现对一个网页的长截图

8. 分屏功能
        
        按住任一打开的窗口左上角的绿色的全屏按钮

9. 批量更改文件名称
10. 屏幕共享 Screen Sharing
11. 录屏 QuickTime Player  
12. gif录制 giphy capture
13. 清理dns缓存
    
    sudo dscacheutil -flushcache