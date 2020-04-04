---
layout:     post
title:      "前端性能优化之浏览器缓存"
subtitle:   " \"browser cache optimized for front-end performance \""
date:      2018-10-12 21:39:58
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---


前端性能优化有很多方式，今天我们主要学习下关于浏览器缓存的一些知识。

**客户端缓存**：

session

localStorage (需要注意安全问题，防篡改)

cookie (数据存储尽量不要使用这种方式存放，请求时会增加头部重量)

图片采用wbp 格式，占用体积小。

  

**浏览器缓存**

**作用：**

1.  **减少冗余的数据传输**
2.  **减少服务器负担**
3.  **加快客户端加载网页的速度**

**当客户端向浏览器发送请求的时候，如果没有缓存则直接请求服务器。如果有则根据缓存状态来是否从缓存中获取对应的文件。**

  

-   **强制缓存**

**利用**Expires和Cache-Control两个字段来控制

expires:是缓存过期时间（即资源过期时间），是个绝对值。所以如果客户端更改时间，会导致缓存混乱。所以http:1.1增加了cache-control：max-age 字段,它是一个相对的时间。

Cache-Control与Expires可以在服务端配置同时启用，同时启用的时候Cache-Control优先级高。

Control:max-age=3600，代表着资源的有效期是3600秒

1.  no-cache：不使用本地缓存。需要使用缓存协商，先与服务器确认返回的响应是否被更改，如果之前的响应中存在ETag，那么请求的时候会与服务端验证，如果资源未被更改，则可以避免重新下载。
2.  no-store：直接禁止游览器缓存数据，每次用户请求该资源，都会向服务器发送一个请求，每次都会下载完整的资源。
3.  public：可以被所有的用户缓存，包括终端用户和CDN等中间代理服务器。
4.  private：只能被终端用户的浏览器缓存，不允许CDN等中继缓存服务器对其缓存。  
      
    

  

-   **协商缓存**

**普通刷新会启动弱缓存（即协商缓存），忽略强缓存。**

**协商缓存涉及到两组header** **字段。**

Etag和If-None-Match

Last-Modified和If-Modified-Since

-   Etag和If-None-Match

他们返回的是一个校验码，是个hash值。Etag能够保证资源都是唯一的，是客户端请求服务器的时候服务器返回的。如果

资源变化了Etag 也会发生变化。当再次请求服务器的时候服务器根据浏览器上送的If-None-Match（之前服务器返回的etag值）值来判断是否命中缓存。

  

-   Last-Modified和If-Modified-Since

浏览器第一次请求一个资源的时候，服务器返回的header中会加上Last-Modify，Last-modify是一个时间标识该资源的最后修改时间，例如Last-Modify: Thu,31 Dec 2037 23:59:59 GMT。

当浏览器再次请求该资源时，request的请求头中会包含If-Modify-Since，该值为缓存之前返回的Last-Modify。服务器收到If-Modify-Since后，根据资源的最后修改时间判断是否命中缓存。

如果命中缓存，则返回304，并且不会返回资源内容，并且不会返回Last-Modify。

Last-Modified与ETag是可以一起使用的，**服****务****器会****优****先****验证****ETag**，一致的情况下，才会继续比对Last-Modified，最后才决定是否返回304。

Etag的优势：

HTTP1.1中Etag的出现主要是为了解决几个Last-Modified比较难解决的问题：

-   一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET；
-   某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)；
-   某些服务器不能精确的得到文件的最后修改时间。

  

  

  

参考资料：[http://caibaojian.com/http-cache-3.html](http://caibaojian.com/http-cache-3.html)