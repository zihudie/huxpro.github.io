---
layout:     post
title:      "关于闭包的理解总结"
subtitle:   " \"abhout closure\""
date:      2019-01-06 14:43:39
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---



  
  # 闭包实现方法
  
  // 实现函数 makeClosures，调用之后满足如下条件：
    // 1、返回一个函数数组 result，长度与 arr 相同
    // 2、运行 result 中第 i 个函数，即 result[i]()，结果与 fn(arr[i]) 相同
    var arr = [1, 2, 3]
    

    function fn(x) {
        return x * x;
    }

    function makeClosures(arr, fn) {
        var result = []
        // ways1:
        // for (var i = 0; i < arr.length; i++) {
            
        //     var curFn = function (val) {
        //         return function () {
        //            return  fn(val)
        //         }
        //     }
        //     result.push(curFn(arr[i]))
        // }

        // ways2 :
        // for(let i=0; i<arr.length;i++){
        //      result[i] = function(){
        //          return fn(arr[i])
        //      }
        // }
        // return result

        // ways3:

         //for(var i=0;i<arr.length;i++){
        //         result[i] = function(num){
        //             return function(){
        //                 return fn(num); 
       //             }
       //         }(arr[i]);
      //     }
      //     return result

     }

    makeClosures(arr, fn)
    console.log(result)
    console.log(result[1]())
    
# 闭包概念


函数嵌套函数，内部函数总是可以访问其所在的外部函数中声明的参数和变量，即使在其外部函数被返回（寿命终结）了之后。

闭包本质还是函数，只不过这个函数绑定了上下文环境（函数内部引用的所有变量）。


关于为什么在 JavaScript 中闭包的应用都有关键词“return”，引用 JavaScript 秘密花园中的一段话：


> 闭包是js中非常重要的特性，这意味着当前作用域总是能够访问外部作用域中的变量。因为函数是js 中唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

https://www.cnblogs.com/wx1993/p/7717717.html


# 应用

*  父作用域想访问子作用域的变量

*  保护函数内的变量安全：如迭代器、生成器。

```
var Foo = function(name){
      var name = name;
      var age = 12;
      this.getName = function(){
          console.log(name)
          return name;
      };
      this.getAge = function(){
          return age;
      };
  };
  
  var foo = new Foo("yatou");
  var foo1 = new Foo("xiaobai");
```

* 在内存中维持变量：如果缓存数据、柯里化

# 注意

需要注意的，由于闭包内的部分资源无法自动释放，容易造成内存泄露
