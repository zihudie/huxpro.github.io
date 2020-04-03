---
layout:     post
title:      "深入理解原型链和面向对象的继承"
subtitle:   " \"深入理解原型链和面向对象的继承\""
date:       2018-05-06  19:38:26 
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---

说实话关于原型链和面向对象我已经看过很多次了，对看过很多次了，但是依旧不能清晰的表达出他们直接的关系【理解不深】。类似于网上大家说的关于看过一本书，让你说出这本书说了什么了，你吞吞吐吐的回答道：嗯………就是那个那个……嗯…………。好，结束，对说不出来。今天我就自己来分析分析，下次你来问我，我讲给你听啊~~~^_^

创建对象的几种方式

![enter image description here](https://img-blog.csdn.net/2018042918191377?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![enter image description here](https://img-blog.csdn.net/2018042918191377?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180429181853243?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

针对上面贴出来的代码我们说几个概念

 构造函数，原型，实例，原型对象，原型链

1.构造函数：new 后面的函数fn就是构造函数

2.实例：o1,o2,o3 对象都是实例。

3.函数的原型prototype：函数一创建就有prototype，prototype是一个对象，指向了当前构造函数的引用地址。

4.函数的原型对象__proto__：所有对象都有__proto__属性， 当用构造函数实例化（new）一个对象时，会将新对象的__proto__属性指向 构造函数的prototype。

![](https://img-blog.csdn.net/20180429182417640?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

下面我们用一张图来说明他们之间的联系：

![](https://img-blog.csdn.net/20180506182851429)



实例对象o2通过构造函数fn 用new方法来创建，构造函数fn通过prototype属性访问它的原型对象，原型对象的constructor属性指向构造函数。

实例对象o2通过__proto__属性访问fn的原型对象，fn的原型对象又通过__proto__ 查找它上一级的原型对象，这样一级一级往上查找，直至Object的原型。整个过程我们称之为原型链。

原型链之instanceof 
判断实例对象的__proto__属性是否跟构造函数的prototype以及它的上级原型对象相关联【详细可看其他文章】

 - 面向对象的继承
 ```
  /**
     * [构造函数实现继承]
     * @return {[type]} [description]
     * 缺点：只能继承父类构造函数里的属性方法，原型链上的属性和方法访问不到
     */
    function c1 (){
        p1.call(this)
    }
    p1.prototype.say = function(){
        console.log("say")
    }
    var t1 = new c1()
   /**
    * [c2 原型链实现继承]
    * @return {[type]} [description]
    * 缺点： 子类如果创建多个对象，某一个对象将父类的属性改变，则其他对象也相应的会改变，
    */
   
   function p2 (){
       this.name = "parent2";
       this.type = [1,2,3]
   }
    function c2 (){
        this.age = 3
    }
    c2.prototype = new p2()
    c2.prototype.say = function (){
        console.log(1)
    } 
    var t2 = new c2()
    var t2_1 = new c2()
    t2.type.push(4)
    console.log(t2_1.type)  // [1,2,3,4]
 
 
    /**
     * [构造函数+原型链实现]
     *
     *缺点： 父类函数执行了两次
     * @return {[type]} [description]
     */
    function p3 (){
        this.name = "parent3";
        this.type = [1,2,3]
    }
    function c3 (){
        p3.call(this)
        this.age = 3
    }
    c3.prototype = new p3()
    c3.prototype.say = function (){
        console.log(1)
    } 
    var t3 = new c3()
    var t3_1 = new c3()
    t3.type.push(4)
    console.log(t3_1.type)  // [1,2,3]
   
   /**
     * [构造函数+原型链实现 优化]
     * 缺点:无法判断当前对象是由子类创建的还是父类创建的
     * @return {[type]} [description]
     */
    function p4 (){
        this.name = "parent3";
        this.type = [1,2,3]
    }
    function c4 (){
        p4.call(this)
        this.age = 3
    }
    c4.prototype = p4.prototype
 
 
    c4.prototype.say = function (){
        console.log(1)
    } 
    var t4 = new c4()
    var t4_1 = new c4()
    t4.type.push(4)
    console.log(t4_1.type)  // [1,2,3]
    console.log(t4.constructor ) // p4
 
 
 
 
    /**
      * [构造函数+原型链实现 优化2]
      * 完美
      * @return {[type]} [description]
      */
     function p5 (){
         this.name = "parent5";
         this.type = [1,2,3]
     }
     function c5 (){
         p5.call(this)
         this.age = 3
     }
     c5.prototype = Object.create(p5.prototype)
    
     p5.prototype.constructor = c5
    
     var t5 = new c5()
     var t5_1 = new c5()
     ```















