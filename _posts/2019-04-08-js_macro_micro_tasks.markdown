---
layout:     post
title:      "js 宏观任务和微观任务"
subtitle:   " \"javascript Macro tasks and micro tasks\""
date:      2019-04-09 00:47:45
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---



> 这篇文章是通过学习极客时间winter大大的《重学前端》中16| promise里的代码为什么比setTimeout先执行总结而来

宏观任务[MacroTask] 和  微观任务[MicroTask]
> 第一次知道这个概念
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190408165620808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==,size_16,color_FFFFFF,t_70)

 
## promise
 - 

```
 var r = new Promise(function(resolve, reject) {
      console.log("a");
      resolve();
    });
    setTimeout(() => console.log("d"), 0);
    r.then(() => console.log("c"));
    console.log("b");

    // 执行顺序 a b c d
```
-
```

    setTimeout(() => console.log("d"), 0);
    var r1 = new Promise(function(resolve, reject) {
      resolve();
    });
    r1.then(() => {
      var begin = Date.now();
      while (Date.now() - begin < 1000);
      console.log("c1");
      new Promise(function(resolve, reject) {
        resolve();
      }).then(() => console.log("c2"));
    });
    // 虽然第二个promise 间隔了1秒 但是还是 先于setTimeout执行
    // c1  c2  d
    
```
 >  通过一系列的实验，我们可以总结一下如何分析异步执行的顺序：
     首先我们分析有多少个宏任务；
    在每个宏任务中，分析有多少个微任务；
     根据调用次序，确定宏任务中的微任务执行次序；
     根据宏任务的触发规则和调用次序，确定宏任务的执行次序；
    确定整个顺序。
```
function sleep(duration) {
      return new Promise(function(resolve, reject) {
        console.log("b");
        setTimeout(resolve, duration);
      });
    }
    console.log("a");
    sleep(5000).then(() => console.log("c"));

    // 将setTimeout封装成可以用于异步的函数
 
```
## async/await
-- async用来表示函数是异步的，定义的函数会返回一个promise对象，可以使用then方法添加回调函数。我们把所有返回 Promise 的函数都可以认为是异步函数。[所以它也是微观任务]

-- await 后面可以跟任何的JS 表达式。虽然说 await 可以等很多类型的东西，但是它最主要的意图是用来等待 Promise 对象的状态被 resolved。如果await的是 promise对象会造成异步函数停止执行并且等待 promise 的解决,如果等的是正常的表达式则立即执行。
```
   var obj = document.getElementById("animate");
    function timeSleep(times) {
      return new Promise((resolve, reject) => {
        setTimeout(resolve, times);
      });
    }
   async function colorChange(color, times) {
      obj.style.backgroundColor = color;
      await timeSleep(times);
    }

    async function trafciLigth() {
      // while (true) {
      await colorChange("#27ED4E", 3000);
      await colorChange("red", 2000);
      await colorChange("yellow", 1000);
      // }
    }
    trafciLigth();
```
参考这篇文章也可以帮助理解https://www.jianshu.com/p/4516ad4b3048