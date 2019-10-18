---
title: "IO Model"
date: 2019-10-02T09:02:42+08:00
categories: 2019-10
tags: [I/O]
author: sgl
---

> 这里使用udp举例，因为udp状态简单：要么收到数据，要么没有收到；tcp协议相对比较复杂

## 五种I/O模型

+ 阻塞I/O Blocking I/O

+ 非阻塞I/O Non blocking I/O

+ 多路复用 I/O multiplexing (select and poll)

+ 信号驱动 Signal driving I/O(SIGIO)

+ 异步I/O Asynchronous I/O

## 两个阶段
* 第一阶段：wait for data 等待网络里的数据到达，到达后，数据被暂存在内核空间的内核缓冲区
* 第二阶段：copy data from kernel to user 把数据从内核空间的内核缓冲区拷贝到用户空间的应用进程缓冲区

## 两个缓冲区

- 内核缓冲区

- 进程缓冲区

## 同步与异步
> 描述的是用户线程与内核的交互方式

- 同步指用户线程发起I/O请求后需要等待或者轮询内核I/O操作完成后才能继续执行
- 异步是指用户线程发起I/O请求后仍然继续执行，当内核I/O操作完成后会通知用户线程，或者调用用户线程注册的回调函数

## 阻塞与非阻塞
> 描述是用户线程调用内核I/O操作的方式，

+ 阻塞是指I/O操作需要彻底完成后才返回到用户空间
+ 非阻塞是指I/O操作被调用后立即返回给用户一个状态值，无需等到I/O操作彻底完成

## 系统调用
> 指运行在用户空间的程序向操作系统内核请求需要更高权限运行的服务。当你的代码需要做I/O操作(open、read、write)、或者是进行内存操作、甚至是说要获取一个系统时间，就需要通过系统调用来和内核进行交互
> 系统调用（system call），指运行在用户空间的程序向操作系统内核请求需要更高权限运行的服务。系统调用提供用户程序与操作系统之间的接口。大多数系统交互式操作需求在内核态运行。如设备IO操作或者进程间通信

+ 开销比用户空间内的函数调用大，因为系统调用时，CPU要做的事情太多；软件要做的事情也太多
+ 用户空间通过系统调用访问内核空间，从而实现对硬盘/网卡等硬件设备的访问
+ 系统调用和普通库函数调用非常相似，只是系统调用由操作系统核心提供，运行于内核态，而普通的函数调用由函数库或用户自己提供，运行于用户态

### 阻塞I/O

两个阶段,全程阻塞

> 见下图，最流行的模型。应用进程调用`recvfrom`(一次系统调用)，然后阻塞，等待两种情况发生：
>    
>     1. 错误返回
>     2. 成功返回，数据首先通过网络到达内核缓冲区，然后再拷贝到进程缓冲区，之后返回

> 系统调用`recvfrom`成功返回后，应用进程开始处理进程缓冲区里的数据

>> 注意：系统调用不是信号中断，因为系统调用会一直阻塞

<div align="center">
    <img src="/img/blog/2019/IO Model-figure_6.1.png" height="300" width="600" >
    <p align="center">图1</p>
</div>


### 非阻塞I/O

第一阶段是非阻塞的，不断检查是否准备好数据；第二阶段阻塞，直到读取数据完成

> 见下图，`recvfrom`系统调用调用之后，有两种结果： 轮询检查内核数据，
>
>     1. 如果数据还没准备好，内核马上返回给进程，但是会返回一个error，此时进程并没有被阻塞。进程在返回之后，可以干点别的事情，然后再发起recvfrom系统调用。
>        重复上面的过程，循环往复的进行recvfrom系统调用。这个过程通常被称之为轮询。
>     2. 如果数据已经准备好，则阻塞，直到从内核中把数据拷贝到进程结束，然后应用进程开始进行数据处理

>> 特点是用户进程完成巡检

>> 缺点是反复轮询，CPU消耗大；轮询期间进行多次系统调用


![](/img/blog/2019/IO Model-figure_6.2.png#pic_center=300x600)

<p align="center">图2</p>

### 多路复用

> 见下图，
>
I/O多路复用实际上就是用select, poll, epoll监听多个io对象，当io对象有变化（有数据）的时候就通知用户进程。
有些地方也称这种IO方式为事件驱动IO(event driven IO)。
与多进程和多线程技术相比，I/O多路复用技术的最大优势是系统开销小，系统不必创建进程/线程，也不必维护这些进程/线程，从而大大减小了系统的开销。

>> 特点是内核进程完成巡检
>
>> 缺点是一个进程监视的socket是有限制的，一般是1024个，当然可以修改；而且存在两次系统调用(select和recvfrom)


![](/img/blog/2019/IO Model-figure_6.3.png#pic_center=300x600)

<p align="center">图3</p>

### 信号
第一阶段构造一个信号处理器，第二阶段阻塞读取数据



![](/img/blog/2019/IO Model-figure_6.4.png)

<p align="center">图4</p>

### 异步I/O

两个阶段，都是非阻塞的




![](/img/blog/2019/IO Model-figure_6.5.png)

<p align="center">图5</p>

### 对比
 
![](/img/blog/2019/IO Model-figure_6.6.png)

<p align="center">图6</p>


### 参考
1. https://zhuanlan.zhihu.com/p/54580385
2. [Chapter 6. I/O Multiplexing: The select and poll Functions](https://notes.shichao.io/unp/ch6/)及[文章的github地址](https://github.com/shichao-an/notes)