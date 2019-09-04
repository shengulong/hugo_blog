---
title: "requests连接池即长连接分析"
date: 2019-07-06T07:39:09+08:00
categories: 2019-07
tags: [requests,长连接]
author: sgl
---

requests长连接
====

### 遗留问题


 - [ ] 为什么requests短连接时，虽然http头中携带了`Connection: keep-alive`头，服务端却回复`Connection: close`头？


> `requests`使用`Session()`即长连接时，请求头会携带字段`Connection: keep-alive`,服务端回复相同的http头
        
> 如下5个http连接公用一个tcp连接
>
>       with requests.Session() as s:
>            s.get("http://www.baidu.com")
>            s.get("http://www.baidu.com")
>            s.get("http://www.baidu.com")
>            s.get("http://www.baidu.com")
>            s.get("http://www.baidu.com")
>
>而下面的请求各自使用一个tcp连接，用完即销毁，请求头里面也有`Connection: keep-alive`头，但是服务端回复`Connection: close`
>
>requests.get("http://www.baidu.com")
>
>requests.get("http://www.baidu.com")
>
>requests.get("http://www.baidu.com")
>
>requests.get("http://www.baidu.com")

### 使用

基本使用方法：申请一个tcp连接，进行多个http请求后，关闭连接
    
    Basic Usage::

      import requests
      s = requests.Session()
      进行多个连接
      s.get('https://httpbin.org/get')      
      s.get('https://httpbin.org/get')
      关闭连接  
      s.close()
    
    Or as a context manager::
        
      with requests.Session() as s:
          进行多个连接  
          s.get('https://httpbin.org/get')      
          s.get('https://httpbin.org/get')          
      
    """
    
### 原理
> 撸下代码

调用顺序：    
    
    requests.Session().get() --> requests.Session().request() --> requests.Session().send()
    
查看`requests包`的`Session()`类`send()`方法的第645行和646行代码，表示调用使用某个适配器的`send()`

    # Send the request
    r = adapter.send(request, **kwargs)

如果使用的默认的`HTTPAdapter()`，查看其`send()`方法，下面的`conn`最终获得一个`HTTPConnectionPool`对象

    try:
        conn = self.get_connection(request.url, proxies)
    except LocationValueError as e:
        raise InvalidURL(e, request=request)
        


最终调用到了`urllib3.connectionpool.HTTPConnectionPool()._get_conn()`

    def _get_conn(self, timeout=None):
        """
        Get a connection. Will return a pooled connection if one is available.

        If no connections are available and :prop:`.block` is ``False``, then a
        fresh connection is returned.

        :param timeout:
            Seconds to wait before giving up and raising
            :class:`urllib3.exceptions.EmptyPoolError` if the pool is empty and
            :prop:`.block` is ``True``.
        """
        conn = None
        try:
            conn = self.pool.get(block=self.block, timeout=timeout)

        except AttributeError:  # self.pool is None
            raise ClosedPoolError(self, "Pool is closed.")

        except queue.Empty:
            if self.block:
                raise EmptyPoolError(self,
                                     "Pool reached maximum size and no more "
                                     "connections are allowed.")
            pass  # Oh well, we'll create a new connection then

        # If this is a persistent connection, check if it got disconnected
        if conn and is_connection_dropped(conn):
            log.debug("Resetting dropped connection: %s", self.host)
            这里关闭了已经无用的连接，比如服务端主动关闭，而requests所在的客户端处于CLOSE_WAIT状态tcp连接
            conn.close()
            if getattr(conn, 'auto_open', 1) == 0:
                # This is a proxied connection that has been mutated by
                # httplib._tunnel() and cannot be reused (since it would
                # attempt to bypass the proxy)
                conn = None

        return conn or self._new_conn()            
        
里面调用了`urllib3.util.connection.is_connection_dropped()`    

    def is_connection_dropped(conn):  # Platform-specific
        """
        Returns True if the connection is dropped and should be closed.
    
        :param conn:
            :class:`httplib.HTTPConnection` object.
    
        Note: For platforms like AppEngine, this will always return ``False`` to
        let the platform handle connection recycling transparently for us.
        """
        sock = getattr(conn, 'sock', False)
        if sock is False:  # Platform-specific: AppEngine
            return False
        if sock is None:  # Connection already closed (such as by httplib).
            return True
        try:
            # Returns True if readable, which here means it's been dropped
            return wait_for_read(sock, timeout=0.0)
        except NoWayToWaitForSocketError:  # Platform-specific: AppEngine
            return False
            
继而调用了`urllib3.util.wait.wait_for_read()`
    
    def wait_for_read(sock, timeout=None):
        """ Waits for reading to be available on a given socket.
        Returns True if the socket is readable, or False if the timeout expired.
        """
        return wait_for_socket(sock, read=True, timeout=timeout)
                   
我们再看下处于CLOSE_WAIT状态的tcp连接，查看Recv-Q(数据已经在本地接收缓冲区,但是还没有recv())的值不为零，是不是刚好呼应了上面的wait_for_read

    Proto Recv-Q Send-Q Local Address         Foreign Address         State           PID/Program name
    tcp        1          0       127.0.0.1:8071          127.0.0.1:46423        CLOSE_WAIT      5782/java
    tcp        1          0       127.0.0.1:8071          127.0.0.1:41958         CLOSE_WAIT      5782/java
    tcp        1          0       127.0.0.1:8071          127.0.0.1:42004        CLOSE_WAIT      5782/java
    tcp        1          0       127.0.0.1:8071          127.0.0.1:52313         CLOSE_WAIT      5782/java                     
    
### 结论

一般情况下，服务端会维持一段时间的长连接，但是一旦超过闲置时间没有连接发生，则主动断开：如下表示闲置时间为5s

    --httpKeepAliveTimeout   = how long idle HTTP keep-alive connections are kept around (in ms; default 5000)

1. 客户端requests长连接时：各个请求共用同一个tcp长连接，但是超过一定时间不使用，则服务端主动断开连接，服务端处于time_wait状态，直至超时后释放资源；
而客户端会长时间处于close_wait状态(因为requests长连接时，不会主动自动发送close报文及FIN报文)，那么这个close_wait到什么时候才关闭呢？
如以上代码走读分析，当新的http请求产生的时候，发现旧的tcp连接不可用(因为已经处于close_wait状态了嘛)，则关闭这个连接，并开启一个新的tcp长连接

        requests对于每个新的请求，都去池子里请求一个连接，在请求的过程中，判断连接是否可用，不可用，关闭，然后创建一个新的连接

2. 客户端requests短连接时：每个请求开启新的tcp连接，完成后，主动断开，这时候，客户端存在大量的time_wait状态，服务端没有问题，会迅速关闭tcp连接    