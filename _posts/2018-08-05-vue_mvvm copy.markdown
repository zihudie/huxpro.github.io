---
layout:     post
title:      "浅谈vue双向绑定原理"
subtitle:   " \"principle of vue mvvm \""
date:      2018-10-12 21:39:58
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---


1.  **简析mvvm框架**
    
    ----------
    

目前angular,reat和vue都是mvvm类型的框架

以vue为例

![](https://img-blog.csdn.net/20180805211859240?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

  

这里的vm 就是vue框架，它相当于中间枢纽的作用,连接着model 和view.

-   当前台显示的view发生变化了，它会实时反应到viewModel上，如果有需要，viewModel 会通过ajax等方法将改变的数据 传递给后台model
-   同时从后台model获取过来的数据，通过vm将值响应到前台UI上

  

-   **双向绑定原理**
    
    ----------
    

![](https://img-blog.csdn.net/20180805211934836?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

vm的核心是view 和 data

-   当data 有变化的时候它通过Object.defineProperty(）方法中的set方法进行监控，并调用在此之前已经定义好data 和view的关系了的回调函数，来通知view进行数据的改变
-   而view 发生改变则是通过底层的input 事件来进行data的响应更改

vue是通过Object.defineProperty()来实现数据劫持的。

Object.defineProperty( )是用来做什么的？它可以来控制一个对象属性的一些特有操作，比如读写权、是否可以枚举，这里我们主要先来研究下它对应的两个描述属性get和set

```javascript
varBook= {}

       varname= '';

       Object.defineProperty(Book, 'name', {

           set:function(value) {

                name= value;

                console.log('你取了一个书名叫做'+ value);

           },

           get:function() {

                return'《'+ name+ '》'

           }

       })



       console.log(Book)

       Book.name= 'vue权威指南'; // 你取了一个书名叫做vue权威指南

        console.log(Book.name); // 《vue权威指南》

       // get 是在读取那么属性的时候触发的

       // set 是在设置属性值的时候触发的
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

**实现方法： 观察者模式**

![](https://img-blog.csdn.net/20180805223511210?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

Observer（Objec.defineProperty中的set）监听data的变化，当data有变化的时候通知观察者列表Dep（里面有与data变化对应的update函数）,watcher负责向观察者列表里添加（订阅）对应的更新函数，Dep里的更新函数执行完了之后将最新的值更新到view上。

具体的代码实现可参考：[https://www.cnblogs.com/libin-1/p/6893712.html](https://www.cnblogs.com/libin-1/p/6893712.html)