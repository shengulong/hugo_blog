---
title: "Golang文件锁"
date: 2019-05-18T22:54:58+08:00
categories: 2019-05
tags: []
author: sgl
---
> 多个goroutine同时操作一同一个文件时，很容易出现数据混乱，因此有必要对文件进行加锁