---
layout:     post
title:      "前后端分离之项目引入node"
subtitle:   " \"前后端分离之项目引入node\""
date:      2018-05-27 16:35:05
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---

## 一、为什么要做前后端分离

随着不同终端(Pad/Mobile/PC)的兴起，对开发人员的要求越来越高，纯浏览器端的响应式已经不能满足用户体验的高要求，我们往往需要针对不同的终端开发定制的版本。为了提升开发效率，前后端分离的需求越来越被重视，**后端负责业务/数据接口，前端负责展现/交互逻辑，同一份数据接口，我们可以定制开发多个版本**。

现阶段我们主要以后端MVC的模式进行开发，这种模式严重阻碍了前端开发效率，也让后端不能专注于业务开发，对后期的维护损耗较大。

**解决方案是让前端能控制Controller层**，但是如果在现有技术体系下很难做到，NodeJS就能很好的解决这个问题。

基于此我们公司的pc项目使用node+express作为了中间层。现在开发一个工程，前期基本靠自己就能自给自足，数据可以使用mock环境来搭建。前端切页面，写逻辑，到了差不多联调接口的时候，基本都可以同步进行了。减少了很多前期沟通时间，效率大大提升。

  

-   node分层会涉及每层之间的通讯，肯定会有一定的性能损耗。单合理的分层能让职责清晰、也方便协作，会大大提高开发效率。分层带来的损失，一定能在其他方面的收益弥补回来。
-   另外，一旦决定分层，我们可以通过优化通讯方式、通讯协议，尽可能把损耗降到最低。

## 二、node+express 使用方法

  

Express 简介

Express 是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

使用 Express 可以快速地搭建一个完整功能的网站。

Express 框架核心特性：

-   可以设置中间件来响应 HTTP 请求。
    
-   定义了路由表用于执行不同的 HTTP 请求动作。
    
-   可以通过向模板传递参数来动态渲染 HTML 页面。
    

1.请求和响应

Express 应用使用回调函数的参数： **request** 和 **response** 对象来处理请求和响应的数据。

```html
app.get('/',function(req,res){
//
})
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

```html
request 和 response 对象的具体介绍：
Request 对象 - request 对象表示 HTTP 请求，包含了请求查询字符串，参数，内容，HTTP 头部等属性。常见属性有：
req.app：当callback为外部文件时，用req.app访问express的实例
req.baseUrl：获取路由当前安装的URL路径
req.body / req.cookies：获得「请求主体」/ Cookies
req.fresh / req.stale：判断请求是否还「新鲜」
req.hostname / req.ip：获取主机名和IP地址
req.originalUrl：获取原始请求URL
req.params：获取路由的parameters
req.path：获取请求路径
req.protocol：获取协议类型
req.query：获取URL的查询参数串
req.route：获取当前匹配的路由
req.subdomains：获取子域名
req.accepts()：检查可接受的请求的文档类型
req.acceptsCharsets / req.acceptsEncodings / req.acceptsLanguages：返回指定字符集的第一个可接受字符编码
req.get()：获取指定的HTTP请求头
req.is()：判断请求头Content-Type的MIME类型
```

```html

Response 对象 - response 对象表示 HTTP 响应，即在接收到请求时向客户端发送的 HTTP 响应数据。常见属性有：
res.app：同req.app一样
res.append()：追加指定HTTP头
res.set()在res.append()后将重置之前设置的头
res.cookie(name，value [，option])：设置Cookie
opition: domain / expires / httpOnly / maxAge / path / secure / signed
res.clearCookie()：清除Cookie
res.download()：传送指定路径的文件
res.get()：返回指定的HTTP头
res.json()：传送JSON响应
res.jsonp()：传送JSONP响应
res.location()：只设置响应的Location HTTP头，不设置状态码或者close response
res.redirect()：设置响应的Location HTTP头，并且设置状态码302
res.render(view,[locals],callback)：渲染一个view，同时向callback传递渲染后的字符串，如果在渲染过程中有错误发生next(err)将会被自动调用。callback将会被传入一个可能发生的错误以及渲染后的页面，这样就不会自动输出了。
res.send()：传送HTTP响应
res.sendFile(path [，options] [，fn])：传送指定路径的文件 -会自动根据文件extension设定Content-Type
res.set()：设置HTTP头，传入object可以一次设置多个头
res.status()：设置HTTP状态码
res.type()：设置Content-Type的MIME类型
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

2.express路由

我们已经了解了 HTTP 请求的基本应用，而路由决定了由谁(指定脚本)去响应客户端请求。

在HTTP请求中，我们可以通过路由提取出请求的URL以及GET/POST参数。

接下来我们扩展 Hello World，添加一些功能来处理更多类型的 HTTP 请求。

创建 express_demo2.js 文件，代码如下所示：

```html
var express = require('express');
var app = express();
 
//  主页输出 "Hello World"
app.get('/', function (req, res) {
   console.log("主页 GET 请求");
   res.send('Hello GET');
})
 
 
//  POST 请求
app.post('/', function (req, res) {
   console.log("主页 POST 请求");
   res.send('Hello POST');
})
 
//  /del_user 页面响应
app.get('/del_user', function (req, res) {
   console.log("/del_user 响应 DELETE 请求");
   res.send('删除页面');
})
 
//  /list_user 页面 GET 请求
app.get('/list_user', function (req, res) {
   console.log("/list_user GET 请求");
   res.send('用户列表页面');
})
 
// 对页面 abcd, abxcd, ab123cd, 等响应 GET 请求
app.get('/ab*cd', function(req, res) {   
   console.log("/ab*cd GET 请求");
   res.send('正则匹配');
})
 
 
var server = app.listen(8081, function () {
 
  var host = server.address().address
  var port = server.address().port
 
  console.log("应用实例，访问地址为 http://%s:%s", host, port)
 
})
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

3.静态文件

  

Express 提供了内置的中间件 **express.static【目前是express唯一内置的中间件】** 来设置静态文件如：图片， CSS, JavaScript 等。

你可以使用 **express.static** 中间件来设置静态文件路径。例如，如果你将图片， CSS, JavaScript 文件放在 public 目录下，你可以这么写：

```html
app.use(express.static('public'));
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

4.在 Express 中使用模板引擎

  

需要在应用中进行如下设置才能让 Express 渲染模板文件：

-   `views`, 放模板文件的目录，比如： `app.set('views', './views')`
-   `view engine`, 模板引擎，比如： `app.set('view engine', 'jade')`

然后安装相应的模板引擎 npm 软件包。

如何使用jade模板

```html
app.set('view engine', 'jade');
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

然后创建一个路由渲染 `index.jade` 文件。如果没有设置 `view engine`，您需要指明视图文件的后缀，否则就会遗漏它。

5.强大的中间件

  

**中间件（Middleware）** 是一个函数，它可以访问请求对象（[request object](http://www.expressjs.com.cn/4x/api.html#req) (`req`)）, 响应对象（[response object](http://www.expressjs.com.cn/4x/api.html#res) (`res`)）, 和 web 应用中处于请求-响应循环流程中的中间件，一般被命名为 `next` 的变量。

中间件的功能包括：

-   执行任何代码。
-   修改请求和响应对象。
-   终结请求-响应循环。
-   调用堆栈中的下一个中间件。

如果当前中间件没有终结请求-响应循环，则必须调用 `next()` 方法将控制权交给下一个中间件，否则请求就会挂起。

Express 应用可使用如下几种中间件：

-   [应用级中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.application)
-   [路由级中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.router)
-   [错误处理中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.error-handling)
-   [内置中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.built-in)
-   [第三方中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.third-party)

## 应用级中间件

应用级中间件绑定到 [app 对象](http://www.expressjs.com.cn/4x/api.html#app) 使用 `app.use()` 和 `app.METHOD()`， 其中， `METHOD` 是需要处理的 HTTP 请求的方法，例如 GET, PUT, POST 等等，全部小写。例如：

```html
var app = express();

// 没有挂载路径的中间件，应用的每个请求都会执行该中间件
app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});

// 挂载至 /user/:id 的中间件，任何指向 /user/:id 的请求都会执行它
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});

// 路由和句柄函数(中间件系统)，处理指向 /user/:id 的 GET 请求
app.get('/user/:id', function (req, res, next) {
  res.send('USER');
});
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

## 错误处理中间件

错误处理中间件有 **4** 个参数，定义错误处理中间件时必须使用这 4 个参数。即使不需要 `next` 对象，也必须在签名中声明它，否则中间件会被识别为一个常规中间件，不能处理错误。

错误处理中间件和其他中间件定义类似，只是要使用 4 个参数，而不是 3 个，其签名如下： `(err, req, res, next)`。

```html
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

参考资料：

http://www.runoob.com/nodejs/nodejs-express-framework.html

http://www.expressjs.com.cn/guide/routing.html










