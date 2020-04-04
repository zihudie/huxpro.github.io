---
layout:     post
title:      "了解cdn"
subtitle:   " \"learn about cdn\""
date:      2019-01-21 19:02:27
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---


 

# cdn

## cdn 是什么？

内容分发网络（content delivery network）；分布式的网络
类似火车票的代售点，以前必须到火车站才能去买到车票，现在各个城市都有对应的多个代售点。当用户需要买车票的时候，可以去就近的代售点购买。这样能节省用户的时间，同时或者站也不会集中很多人流。


cdn = 更智能的镜像（网站完全拷贝）+缓存 + 流量导流（分流）
镜像是什么？更多的是静态站的处理


## cdn 加速原理

- 内容缓存【以空间换时间】

 squid（代理缓存服务器）作为web 服务（ngix,tomcat）
 
 内容缓存到内存和本地文件
 
 页面访问速度极高
 
 分布在全国各地的网络节点（怎么部署的呢）：机房，托管维护，租用服务器。
 
 多线路支持：同时支持电信，网通，联通等多种路线（南电信北联通）
 
 不同于双线机房的双线接入（一套服务接入两套网络【联通或电信】）
 
 减少跨网访问（支持多样的访问。联通用户走联通用户，电信用户走电信用户）

- 适用范围

  静态和更新频率低的内容更适合
  数据流量打的产品更适用（多视频和多图片的网站）
  带宽价格更便宜
  


## cdn 的作用？

使用户可以就近取得所需内容，提高用户访问网站的响应速度

## cdn的具体使用
- 管理功能

  域名列表

  文件刷新
 
  文件预缓存

  目录刷新

  证书管理（https）

- 数据分析
  带宽统计
  流量

  命中率（多少链接是通过cdn分发，不需要回源中转）

  请求数

  状态码

  日志下载
  
  
 - 统计功能
   请求数（用户分布情况）
   命中率
   ISP 运营商

## cdn的缺点

实施过程有些复杂

## cdn 拓扑图

![用户请求过程](https://img-blog.csdnimg.cn/20190121185804539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==,size_16,color_FFFFFF,t_70)
客户端浏览器先检查是否有本地缓存是否过期，如果过期，则向CDN边缘节点发起请求，CDN边缘节点会检测用户请求数据的缓存是否过期，如果没有过期，则直接响应用户请求，此时一个完成http请求结束；如果数据已经过期，那么CDN还需要向源站发出回源请求（back to the source request）,来拉取最新的数据。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190121185903103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==,size_16,color_FFFFFF,t_70)

 





