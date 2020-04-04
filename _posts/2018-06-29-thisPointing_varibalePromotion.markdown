---
layout:     post
title:      "this指向，变量提升"
subtitle:   " \"关于this指向，变量提升以及跨域的解决方案\""
date:      2018-06-29 18:08:00
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---

1.  this
2.  变量提升
3.  关于继承
4.  跨域解决方法

### >> this指向

Js是静态作用域：是在定义阶段就决定好了的，而不是在执行阶段才决定的。  
参考资料：  
[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)  
[https://www.zhihu.com/question/19636194](https://www.zhihu.com/question/19636194)  
  

this 指的是引用那些函数的对象（指向它执行的上下文环境）。一般this的场景

1.全局作用域下的this

如果是在浏览器环境下，this指向window,但是在严格模式下，函数中的this等于undefined  
如果在node环境中，this指向global（但node环境下js文件中的this为{}，也就是module.export）

2.对象中的this  
当用对象调用自己里面定义的方法时，this指向的这个对象。 例如：

```html
var obj = {
  id: 9,
  test: function() {
    return this.id;
  }
};
console.log(obj.test()); //9
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

3.原型链中的this  
通过原型继承指向新创建的对象  
  
4.构造函数中的this

指向实例化的对象

5.call 和 apply和bind中的this  

```javascript
function add(x) {
 return this.a + x 
} 
var obj = { a: 10 } 
var sum = add.call(obj, 8) 
//var sum = add.apply(obj, [8])
console.log(sum) // 18
它的第一个参数是绑定函数的运行环境。
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

>>箭头函数的this：  
默认指向在定义它时,它所处的对象,而不是执行时的对象。

```javascript
<script>
  var obj = {
    say: function () {
      var f1 = () => {
        console.log(this); // obj
        setTimeout(() => {
          console.log(this); // obj
        })
      }
      f1();
    }
  }
  obj.say()
</script>
结果：都是obj
f1继承父级this指代的obj，不管f1有多层箭头函数嵌套，都是obj.
<script>
  var obj = {
    say: function () {
      var f1 = function () {
        console.log(this);    // window, f1调用时,没有宿主对象,默认是window
        setTimeout(() => {
          console.log(this); // window
        })
      };
      f1();
    }
  }
  obj.say()
</script>
结果：window,window
第一个this：f1调用时没有宿主对象，默认是window
第二个this:继承父级的this,父级的this指代的是window
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

## >>声明提升

Es5:  
变量可以提升  
函数表达式没有提升  
函数声明优先于变量声明

```javascript
fun1()
var fun1 = function () {
console.log(1)
}
// VM1327:2 Uncaught TypeError: fun1 is not a function
   // at <anonymous>:2:1
fun2()
var fun2 = function () {
console.log(2)
}

fun3()
var fun3 = function () {
console.log("fun3")
}
function fun3() {
console.log(3)
}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

Es6:  
let const 不存在变量提升(主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为)

Let变量的生命周期，声明阶段-暂时性死区-初始化-赋值阶段，在未初始化阶段就调用就会报错的。

  

## >>继承

**es5:**（之前的总结）

https://blog.csdn.net/weixin_36852235/article/details/80144048

**es6:**

Class 可以通过extends关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。  
class Point {}  
class ColorPoint extends Point {}  
上面代码定义了一个ColorPoint类，该类通过extends关键字，继承了Point类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个Point类。下面，我们在ColorPoint内部加上代码。

```html
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()  }}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

上面代码中，constructor方法和toString方法之中，都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象

  

## >>跨域

为什么会有跨域？  
同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据。  
  
同源是指：  
协议相同  
域名相同  
端口相同  
限制范围：  
**（1） Cookie、LocalStorage 和 IndexDB 无法读取。  
（2） DOM 无法获得。  
（3） AJAX 请求不能发送。**  
虽然这些限制是必要的，但是有时很不方便，合理的用途也受到影响。下面，我将详细介绍，如何规避上面三种限制。  
  
解决跨域有哪些方法？  
Jsonp  
Json+padding:  
动态创建script标签，向服务器XXX发出请求。请求的查询字符串有个一个callback参数，用来指定回调函数的名字。  
1.）原生实现：

  

```javascript
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';


    // 传参并指定回调执行函数为onBack
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=onBack';
    document.head.appendChild(script);


    // 回调执行函数
    function onBack(res) {
        alert(JSON.stringify(res));
    }
 </script>

服务端返回如下（返回时即执行全局函数）：
onBack({"status": true, "user": "admin"})

```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  
2.）jquery ajax：

```javascript
$.ajax({
    url: 'http://www.domain2.com:8080/login',
    type: 'get',
    dataType: 'jsonp',  // 请求方式为jsonp
    jsonpCallback: "onBack",    // 自定义回调函数名
    data: {}
});
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

jsonp缺点：只能实现get一种请求。  
优势：支持老的浏览器  
Cros  
需要浏览器和服务器同时支持，IE不能低于IE10。(IE8+：IE8/9需要使用XDomainRequest对象来支持CORS）)  
  

![](https://img-blog.csdn.net/20180629174053958?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

  

![](https://img-blog.csdn.net/20180629174106942?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

GET http://x.stuq.com:7001/cros 500 (Internal Server Error)  
  
**这里介绍下简单请求，浏览器直接发出cors请求。在头信息中增加Origin字段**  
![](https://img-blog.csdn.net/20180629174236898?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​  
请求头中origin。标识本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据这个值，决定是否同意这次请求。  
如果Origin指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段（详见下文），就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。  
![](https://img-blog.csdn.net/20180629174244977?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​  
（1）Access-Control-Allow-Origin  
该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个*，表示接受任意域名的请求。  
Access-Control-Allow-Credentials  
该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。  
withCredentials 属性  
Cors请求默认不发cookie和HTTP认证信息。如果要发，客户端和服务器端都要设置。同时服务器端的Access-Control-Allow-Origin不能设为*.要与请求时的origin设置一样。一方面，开发者必须在AJAX请求中打开withCredentials属性。  
var xhr = new XMLHttpRequest();  
xhr.withCredentials = true;  
并且这个cookie是服务端设置过的才会上传，要不然是不传的。  
详细见阮老师的这篇文章：  
[http://www.ruanyifeng.com/blog/2016/04/cors.html](http://www.ruanyifeng.com/blog/2016/04/cors.html)  
  
  
3.iframe  
4.cookie  
两个网页一级域名相同，只是二级域名不同，浏览器允许通过设置document.domain共享 Cookie。  
A:a.test.com  
B:b.test.com  
document.domain = 'test.com';  
这种方法只适用于 Cookie 和 iframe 窗口  
  
5.window.name  
浏览器窗口有window.name属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。  
  

6.window.postMessage

主页面设置：popup.postMessage('Hello World!', 'http://bbb.com');

postMessage方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即"协议 + 域名 + 端口"。也可以设为*，表示不限制域名，向所有窗口发送  
父窗口和子窗口都可以通过message事件，监听对方的消息。  
  
// message事件的事件对象event，提供以下三个属性。  
// event.source：发送消息的窗口  
// event.origin: 消息发向的网址  
// event.data: 消息内容  
window.addEventListener('message', function(e) {  
console.log(JSON.parse(e.data))//{msg: "hello world"}  
console.log(e.origin)//http://x.stuq.com:7001  
console.log(e.source)//window  
}, false);  
  
跨域解法大全：  
[https://www.cnblogs.com/roam/p/7520433.html](https://www.cnblogs.com/roam/p/7520433.html)
















