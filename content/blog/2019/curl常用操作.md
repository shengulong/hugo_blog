---
title: "Curl常用操作"
date: 2019-05-13T11:02:34+08:00
categories: 2019-05
author: shengulong

tags: [linux]
---

>curl --help/--manual查看完整信息

1. 使用curl post上传文件，multipart/form-data

    > 如果不设置filename，默认的filename值就是file的名字，本例的"go.sum"

    curl -x 127.0.0.1:6060 -F "appkey=xxxx" -F "projectCode=platformtool" -F "appsecret=xxxx" -F "file=@go.sum;filename=test" http://xxxx.tongdun.me/v1/resource/upload
    
        报文内容：   
        
        POST /v1/resource/upload HTTP/1.1
        Host: xxxx.tongdun.me
        User-Agent: curl/7.54.0
        Accept: */*
        Content-Length: 15262
        Expect: 100-continue
        Content-Type: multipart/form-data; boundary=------------------------7bba7e03d249b584
        Connection: close
        
        --------------------------7bba7e03d249b584
        Content-Disposition: form-data; name="appkey"
        
        xxxx
        --------------------------7bba7e03d249b584
        Content-Disposition: form-data; name="projectCode"
        
        platformtool
        --------------------------7bba7e03d249b584
        Content-Disposition: form-data; name="appsecret"
        
        xxxx
        --------------------------7bba7e03d249b584
        Content-Disposition: form-data; name="file"; filename="test"
        Content-Type: application/octet-stream
        
        cloud.google.com/go v0.34.0/go.mod h1:aQUYkXzVsufM+DwF1aE+0xfcU+56JwCaLick0ClmMTw=
        github.com/BurntSushi/toml v0.3.1 h1:WXkYYl6Yr3qBf1K79EBnL4mak0OimBfB0XUf9Vl28OQ=
        github.com/BurntSushi/toml v0.3.1/go.mod h1:xHWCNGjB5oqiDr8zfno3MHue2Ht5sIBksp03qcyfWMU=
        github.com/DataDog/zstd v1.3.5 h1:DtpNbljikUepEPD16hD4LvIcmhnhdLTiW/5pHgbmp14=
        github.com/DataDog/zstd v1.3.5/go.mod h1:1jcaCB/ufaK+sKp1NBhlGmpz41jOoPQ35bpF36t7BBo=
        
        
        --------------------------7bba7e03d249b584--

2. 使用curl post上传application/json文件

    curl -x 127.0.0.1:6060 -H "Content-Type: application/json" -X POST --data '{"projectCode":"platformtool","location":"/user/xxxx/users/cicd/requirements.txt","type":"personal"}' http://xxxx.tongdun.me/v1/resource/exist?appsecret=xxxx&appkey=xxxx&projectCode=platformtool
        
        报文内容：
        
        POST /v1/resource/exist?appsecret=xxxx&appkey=xxxx&projectCode=platformtool HTTP/1.1
        Host: xxxx.tongdun.me
        User-Agent: curl/7.54.0
        Accept: */*
        Content-Type: application/json
        Content-Length: 107
        Connection: close
        
        {"projectCode":"platformtool","location":"/user/xxxx/users/cicd/requirements.txt","type":"personal"}

3. 使用curl post上传文件，application/x-www-url-encoded

    curl -x 127.0.0.1:6060  -d "action=del" -d "id=12" http://localhost/test
    
    curl -x 127.0.0.1:6060  -d "action=del&id=12" http://localhost/test
    
        POST /test HTTP/1.1
        Host: localhost
        User-Agent: curl/7.54.0
        Accept: */*
        Content-Length: 16
        Content-Type: application/x-www-form-urlencoded
        Connection: close
        
        action=del&id=12
    
    
    curl -x 127.0.0.1:6060  -d '{"action":"del","id":12}' http://localhost/test
    
        POST /test HTTP/1.1
        Host: localhost
        User-Agent: curl/7.54.0
        Accept: */*
        Content-Length: 24
        Content-Type: application/x-www-form-urlencoded
        Connection: close
        
        {"action":"del","id":12}
        
4. 显示时间：
    
    curl -v --trace-time https://www.baidu.com
    
5. 结束请求后输出信息：

    curl -w "Type: %{content_type}\nCode: %{response_code}\n" https://www.baidu.com

6. 分块下载
    
    > 0-499     specifies the first 500 bytes      
    > 500-999   specifies the second 500 bytes        
    > -500      specifies the last 500 bytes        
    > 9500-     specifies the bytes from offset 9500 and forward        
    > 0-0,-1    specifies the first and last byte only(*)(HTTP)       
    > 100-199,500-599 specifies two separate 100-byte ranges(*) (HTTP)

        curl -r  0-10240  -o "zhao.part1"  http://mydomain.net/~zzh/zhao1.mp3 
        & curl -r 10241-20480  -o "zhao.part1"  http://mydomain.net/~zzh/zhao1.mp3 
        & curl -r 20481 -40960  -o "zhao.part1"  http://mydomain.net/~zzh/zhao1.mp3 
        & curl -r 40961- -o  "zhao.part1"  http://mydomain.net/~zzh/zhao1.mp3
        
        合成一个
        cat zhao.part* > zhao.mp3

7. 断点续传-C，

8. 循环下载，循环匹配下载

    curl -O http://mydomain.net/~zzh/screen[1-10].JPG
    
    curl -O http://mydomain.net/~{zzh,nick}/[001-201].JPG     # >like zzh/001.JPG
    
9. 循环引用下载

    curl -o #2_#1.jpg http://mydomain.net/~{zzh,nick}/[001-201].JPG # like >001_zzh.jpg
    
10. 注意选项-o和-O的区别，-O自动获取远程文件名字作为本地文件名字

    curl -o home.html  http://www.sina.com.cn  当前目录生成本地文件home.html
    
    curl -O http://www.mydomain.com/linux/index.html     当前目录生成本地文件index.html
    
11. 显示抓取错误

        curl -f http://www.sina.com.cn/asdf
        curl: (22) The requested URL returned error: 404 Not Found
        
12. 伪造来源referer

        curl -e www.coolsite.com http://www.showme.com/                     