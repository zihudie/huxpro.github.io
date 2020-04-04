---
layout:     post
title:      "性能优化之throttle, debounce"
subtitle:   " \"throttle, debounce \""
date:     2018-10-14 11:49:30
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---

throttle节流的思想：

应用场景：

**主要****应****用在scroll ， resize****这****种****应****用****场****景中**。在浏览器中像mousemove,mouseenter,scroll,resize 这类事件会频繁的触发，如果不作截流设置性能会极大下降。

```
 const throttle = (func, limit) => {
      let lastFunc
      let lastRan
      return function() {
        const context = this
        const args = arguments
        if (!lastRan) {
          func.apply(context, args)
          lastRan = Date.now()
        } else {
          clearTimeout(lastFunc)
          lastFunc = setTimeout(function() {
            if ((Date.now() - lastRan) >= limit) {
              func.apply(context, args)
              lastRan = Date.now()
            }
          }, limit-(Date.now() - lastRa))
        }
      }
    }
  
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

debounce：防抖的思想：

```
    const  debounce  = (fn,delay)=>{
        let timer ;
        delay = delay || 200
        return function(){
          let ctx = this
          let args = arguments
          if(timer) clearTimeout(timer)
          timer = setTimeout(()=>{
             fn.apply(ctx,args)
          },delay)
        }  
    }
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

应用场景：

**主要****应****用在****类****似****百度首页的输入联想词请求中**。如果每输入一个字就向后台发送请求，会造成请求的极大浪费。因为最终我们想要的结果是最后输入的字段所匹配出来的结果。

以下是测试地址：

Demo地址：

[https://jsbin.com/wopojojiti/2/edit?html,console,output](https://jsbin.com/wopojojiti/2/edit?html,console,output)