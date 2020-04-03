---
layout:     post
title:      "Javascript异步编程方法有哪些"
subtitle:   " \"Javascript异步编程方法有哪些\""
date:      2018-05-13 23:55:12 
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---


Javascript 语言的执行环境是“单线程”（single thread）。所谓“单线程”，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务。

  

这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无响应（假死），往往就是因为某一段 JavaScript 代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。

  

JavaScript 语言本身并不慢，慢的是读写外部数据，比如等待 Ajax 请求返回结果。这个时候，如果对方服务器迟迟没有响应，或者网络不通畅，就会导致脚本的长时间停滞。

  

为了解决这个问题，Javascript 语言将任务的执行模式分成两种：同步（Synchronous）和异步（Asynchronous）。“同步模式”就是传统做法，后一个任务等待前一个任务结束，然后再执行，程序的执行顺序与任务的排列顺序是一致的、同步的。这往往用于一些简单的、快速的、不涉及 IO 读写的操作。

  

“异步模式”则完全不同，每一个任务分成两段，第一段代码包含对外部数据的请求，第二段代码被写成一个回调函数，包含了对外部数据的处理。第一段代码执行完，不是立刻执行第二段代码，而是将程序的执行权交给第二个任务。等到外部数据返回了，再由系统通知执行第二段代码。所以，程序的执行顺序与任务的排列顺序是不一致的、异步的。

  

"异步模式"非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，"异步模式"甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有http请求，服务器性能会急剧下降，很快就会失去响应。

## **一、回调函数**

这是异步编程最基本的方法。

假定有两个函数f1和f2，后者等待前者的执行结果。

f1();

f2();

如果f1是一个很耗时的任务，可以考虑改写f1，把f2写成f1的回调函数。

function f1(callback){

setTimeout(function () {

// f1的任务代码

callback();

}, 1000);

}

执行代码就变成下面这样：

f1(f2);

采用这种方式，我们把同步操作变成了异步操作，f1不会堵塞程序运行，相当于先执行程序的主要逻辑，将耗时的操作推迟执行。

回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度[耦合](http://en.wikipedia.org/wiki/Coupling_(computer_programming))（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。

  

  

## **二、事件监听**

另一种思路是采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

什么是事件驱动模式：

![](https://img-blog.csdn.net/20180513235159170)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

还是以f1和f2为例。首先，为f1绑定一个事件（这里采用的jQuery的[写法](http://api.jquery.com/on/)）。

f1.on('done', f2);

上面这行代码的意思是，当f1发生done事件，就执行f2。然后，对f1进行改写：

```html
function f1(){

setTimeout(function () {

// f1的任务代码

f1.trigger('done');

}, 1000);

}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

f1.trigger('done')表示，执行完成后，立即触发done事件，从而开始执行f2。

这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以["去耦合"](http://en.wikipedia.org/wiki/Decoupling)（Decoupling），有利于实现[模块化](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。

  

## **三、发布/订阅**

上一节的"事件"，完全可以理解成"信号"。

我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做["发布/订阅模式"](http://en.wikipedia.org/wiki/Publish-subscribe_pattern)（publish-subscribe pattern），又称["观察者模式"](http://en.wikipedia.org/wiki/Observer_pattern)（observer pattern）。

首先，f2向"信号中心"jQuery订阅"done"信号。

jQuery.subscribe("done", f2);

然后，f1进行如下改写：

function f1(){

setTimeout(function () {

// f1的任务代码

**jQuery.publish("done");**

}, 1000);

}

jQuery.publish("done")的意思是，f1执行完成后，向"信号中心"jQuery发布"done"信号，从而引发f2的执行。

此外，f2完成执行后，也可以取消订阅（unsubscribe）。

jQuery.unsubscribe("done", f2);

这种方法的性质与"事件监听"类似，但是明显优于后者。因为我们可以通过查看"消息中心"，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。

发布—订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知。在JavaScript 开发中，我们一般用事件模型来替代传统的发布—订阅模式**。**

**eg1:销售处订阅购房信息**

[**http://jsbin.com/xoqabuleni/2/edit?html,js,console,output**](http://jsbin.com/xoqabuleni/2/edit?html,js,console,output)

**eg2:综合**

[**http://jsbin.com/ledugoxaki/5/edit?html,js,console,output**](http://jsbin.com/ledugoxaki/5/edit?html,js,console,output)

## **四、Promises对象**

Promises对象是CommonJS工作组提出的一种规范，目的是为异步编程提供[统一接口](http://wiki.commonjs.org/wiki/Promises/A)。

### **4.1那什么是common js?**

[**JavaScript**](http://lib.csdn.net/base/18)早期主要用于基于浏览器的应用，随着NodeJS的应用，JavaScript被大量应用于服务端应用。但因为客户端和服务端的不同，需要写多份不同的代码以适应客户端和服务端的不同。

CommonJS是一种思想，它的终极目标是使应用程序开发者根据CommonJS API编写的JavaScript应用可以在不同的JavaScript解析器和HOST环境上运行。目前，有四大平台支持CommonJS API：Rhino、Spidermonkey、v8、JavaScriptCore。

如果你写的JavaScript是根据CommonJS API编写的，那么，你就可以在与CommonJS兼容的系统上，用JavaScript做下面这些事情：

· 编写服务端应用；

· 编写命令行工具；

· 编写基于GUI的桌面应用；

· 混合应用程序；

· ![](https://img-blog.csdn.net/20180513235231849)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

只要能够提供这四个变量，浏览器就能加载 CommonJS 模块。

下面是一个简单的示例。

  

```html
var module = {

  exports: {}

};

 

(function(module, exports) {

  exports.multiply = function (n) { return n * 1000 };

}(module, module.exports))

 

var f = module.exports.multiply;

f(5) // 5000

 
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

上面代码向一个立即执行函数提供 module 和 exports 两个外部变量，模块就放在这个立即执行函数里面。模块的输出值放在 module.exports 之中，这样就实现了模块的加载。

CommonJS是一种规范，NodeJS是这种规范的实现

Promise对象是CommonJS工作组提出的一种规范，目的是为异步操作提供[统一接口](http://wiki.commonjs.org/wiki/Promises/A)。

### **4.2那么，什么是Promises？**

首先，它是一个对象，也就是说与其他JavaScript对象的用法，没有什么两样；其次，它起到代理作用（proxy），充当异步操作与回调函数之间的中介。它使得异步操作具备同步操作的接口，使得程序具备正常的同步运行的流程，回调函数不必再一层层嵌套。

简单说，它的思想是，每一个异步任务立刻返回一个Promise对象，由于是立刻返回，所以可以采用同步操作的流程。这个Promises对象有一个then方法，允许指定回调函数，在异步任务完成后调用。

  

比如，异步操作f1返回一个Promise对象，它的回调函数f2写法如下。

```html
(new Promise(f1)).then(f2);
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

总的来说，传统的回调函数写法使得代码混成一团，变得横向发展而不是向下发展。Promises规范就是为了解决这个问题而提出的，目标是使用正常的程序流程（同步），来处理异步操作。它先返回一个Promise对象，后面的操作以同步的方式，寄存在这个对象上面。等到异步操作有了结果，再执行前期寄放在它上面的其他操作。

Promises原本只是社区提出的一个构想，一些外部函数库率先实现了这个功能。ECMAScript 6将其写入语言标准，因此目前JavaScript语言原生支持Promise对象。

  

### **4.3Promise接口**

前面说过，Promise接口的基本思想是，异步任务返回一个Promise对象。

  

Promise对象只有三种状态。

异步操作“未完成”（pending）

异步操作“已完成”（resolved，又称fulfilled）

异步操作“失败”（rejected）

  

这三种的状态的变化途径只有两种。

异步操作从“未完成”到“已完成”

异步操作从“未完成”到“失败”。

这种变化只能发生一次，一旦当前状态变为“已完成”或“失败”，就意味着不会再有新的状态变化了。因此，Promise对象的最终结果只有两种。

  

异步操作成功，Promise对象传回一个值，状态变为resolved。

异步操作失败，Promise对象抛出一个错误，状态变为rejected。

  

Promise对象使用then方法添加回调函数。then方法可以接受两个回调函数，第一个是异步操作成功时（变为resolved状态）时的回调函数，第二个是异步操作失败（变为rejected）时的回调函数（可以省略）。一旦状态改变，就调用相应的回调函数。

```html
// po是一个Promise对象

po.then(

  console.log( ),

  console.error( )

);
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

上面代码中，Promise对象po使用then方法绑定两个回调函数：操作成功时的回调函数console.log，操作失败时的回调函数console.error（可以省略）。这两个函数都接受异步操作传回的值作为参数。

then方法可以链式使用。

```html
po                 

  .then(step1 )      

  .then(step2)       

  .then(step3)       

  .then(             

    console.log( ) ,   

    console.error( )   

  );
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

上面代码中，po的状态一旦变为resolved，就依次调用后面每一个then指定的回调函数，每一步都必须等到前一步完成，才会执行。最后一个then方法的回调函数console.log和console.error，用法上有一点重要的区别。console.log只显示回调函数step3的返回值，而console.error可以显示step1、step2、step3之中任意一个发生的错误。也就是说，假定step1操作失败，抛出一个错误，这时step2和step3都不会再执行了（因为它们是操作成功的回调函数，而不是操作失败的回调函数）。Promises对象开始寻找，接下来第一个操作失败时的回调函数，在上面代码中是console.error。这就是说，Promises对象的错误有传递性。

从同步的角度看，上面的代码大致等同于下面的形式。

  

```html
try {

  var v1 = step1(po);

  var v2 = step2(v1);

  var v3 = step3(v2);

  console.log(v3);

} catch (error) {

  console.error(error);

}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

Promise对象的生成

  

ES6提供了原生的Promise构造函数，用来生成Promise实例。

下面代码创造了一个Promise实例。

```html
var promise = new Promise(function(resolve, reject) {

  // 异步操作的代码

  if (/* 异步操作成功 */){

    resolve(value);

  } else {

    reject(error);

  }

});
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由JavaScript引擎提供，不用自己部署。

  

resolve函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从Pending变为Resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；reject函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从Pending变为Rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

  

Promise实例生成以后，可以用then方法分别指定Resolved状态和Reject状态的回调函数。then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。

![](https://img-blog.csdn.net/20180513235258939)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

demo1:

[http://jsbin.com/henihaxovu/edit?html,js,console,output](http://jsbin.com/henihaxovu/edit?html,js,console,output)

  

demo2:

[http://jsbin.com/yeqakihoto/edit?html,js,console,output](http://jsbin.com/yeqakihoto/edit?html,js,console,output)

  

demo3:

[http://jsbin.com/qorisunoka/1/edit?html,js,console,output](http://jsbin.com/qorisunoka/1/edit?html,js,console,output)

  

  

**4.4小结**

Promise对象的优点在于，让回调函数变成了规范的链式写法，程序流程可以看得很清楚。它的一整套接口，可以实现许多强大的功能，比如为多个异步操作部署一个回调函数、为多个回调函数中抛出的错误统一指定处理方法等等。

而且，它还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是，编写和理解都相对比较难。

## **五、generator方法**

  

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做["协程"](https://en.wikipedia.org/wiki/Coroutine)（coroutine），意思是多个线程互相协作，完成异步任务。

协程有点像函数，又有点像线程。它的运行流程大致如下。

第一步，协程A开始执行。

第二步，协程A执行到一半，进入暂停，执行权转移到协程B。

第三步，（一段时间后）协程B交还执行权。

第四步，协程A恢复执行。

  
function  asnycJob()  {

// ...其他代码

var f = yield readFile(fileA);

// ...其他代码

}

上面代码的函数 asyncJob 是一个协程，它的奥妙就在其中的 yield 命令。它表示执行到此处，执行权将交给其他协程。也就是说，yield命令是异步两个阶段的分界线。

协程遇到 yield 命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除yield命令，简直一模一样。

  

1. yield关键字可以让当前函数暂停执行并保存现场，并跳出到调用此函数的代码处继续执行。

2. 可以利用函数执行时的返回句柄的next方法回到之前暂停处继续执行

3. next执行的返回值的value即是yield关键字后面部分的表达式结果

4. 下一个next的唯一参数值可以作为yield的整体返回值，并赋值给a变量

看下执行顺序就能比较清楚Generator是怎么工作的了

![](https://img-blog.csdn.net/20180513235324504)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

形式上，Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式，定义不同的内部状态（yield在英语里的意思就是“产出”）。

yield表达式只能用在 Generator 函数里面，用在其他地方都会报错。

```html
function* helloWorldGenerator() {

  yield 'hello';

  yield 'world';

  return 'ending';

}

var hw = helloWorldGenerator();
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

上面代码定义了一个 Generator 函数helloWorldGenerator，它内部有两个yield表达式（hello和world），即该函数有三个状态：hello，world 和 return 语句（结束执行）。

然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

```html
hw.next()

// { value: 'hello', done: false }

hw.next()

// { value: 'world', done: false }

hw.next()

// { value: 'ending', done: true

hw.next()

// { value: undefined, done: true }
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

  

上面代码一共调用了四次next方法。

demo：

[http://jsbin.com/zowasopamo/edit?html,js,console,output](http://jsbin.com/zowasopamo/edit?html,js,console,output)

第一次调用，Generator 函数开始执行，直到遇到第一个yield表达式为止。next方法返回一个对象，它的value属性就是当前yield表达式的值hello，done属性的值false，表示遍历还没有结束。

第二次调用，Generator 函数从上次yield表达式停下的地方，一直执行到下一个yield表达式。next方法返回的对象的value属性就是当前yield表达式的值world，done属性的值false，表示遍历还没有结束。

第三次调用，Generator 函数从上次yield表达式停下的地方，一直执行到return语句（如果没有return语句，就执行到函数结束）。next方法返回的对象的value属性，就是紧跟在return语句后面的表达式的值（如果没有return语句，则value属性的值为undefined），done属性的值true，表示遍历已经结束。

第四次调用，此时 Generator 函数已经运行完毕，next方法返回对象的value属性为undefined，done属性为true。以后再调用next方法，返回的都是这个值。

总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束。

再看一个通过next方法的参数，向 Generator 函数内部输入值的例子

demo:

[http://jsbin.com/sefafakofe/edit?html,js,console,output](http://jsbin.com/sefafakofe/edit?html,js,console,output)

![](https://img-blog.csdn.net/20180513235401234)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​














