---
layout:     post
title:      "浏览器工作原理之从请求到页面绘制"
subtitle:   " \"from request to page render\""
date:      2019-04-19 00:47:45
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---


> 这篇文章是通过学习极客时间winter大大的《重学前端》中  浏览器是如何工作的 系列的总结

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190412185342691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==,size_16,color_FFFFFF,t_70)
###  构建dom树过程
![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zdGF0aWMwMDEuZ2Vla2Jhbmcub3JnL3Jlc291cmNlL2ltYWdlLzM0LzVhLzM0MjMxNjg3NzUyYzExMTczYjc3NzZiYTVmNGEwZTVhLnBuZw?x-oss-process=image/format,png)
### 词法解析

```
<p class="a">text text text</p>
```
> 如果我们从最小有意义单元的定义来拆分，第一个词（token）是什么呢？显然，作为一个词（token），整个 p 标签肯定是过大了（它甚至可以嵌套）。

那么，只用 p 标签的开头是不是合适吗？我们考虑到起始标签也是会包含属性的，最小的意义单元其实是“<p” ，所以“ <p” 就是我们的第一个词（token）。

我们继续拆分，可以把这段代码依次拆成词（token）：

<p“标签开始”的开始；
class=“a” 属性；

'>'“标签开始”的结束；
text text text 文本；
</p> 标签结束。
这是一段最简单的例子，类似的还有什么呢？现在我们可以来来看看这些词（token）长成啥样子：

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zdGF0aWMwMDEuZ2Vla2Jhbmcub3JnL3Jlc291cmNlL2ltYWdlL2Y5Lzg0L2Y5ODQ0NGFhM2VhNzQ3MWQyNDE0ZGQ3ZDBmNWUzYTg0LnBuZw?x-oss-process=image/format,png)
用状态机进行词法解析，把每个词的“特征字符”逐个拆开独立的状态，然后组合起来形成联通图。


### DOM构建
构建 DOM 树通过栈来实现：
```
function HTMLSyntaticalParser(){
    var stack = [new HTMLDocument];
    this.receiveInput = function(token) {
        //……
    }
    this.getOutput = function(){
        return stack[0];
    }
}


```

- 栈顶元素就是当前节点；
- 遇到属性，就添加到当前节点；
- 遇到文本节点，如果当前节点是文本节点，则跟文本节点合并，否则入栈成为当前节点的子节点；
- 遇到注释节点，作为当前节点的子节点；
- 遇到 tag start 就入栈一个节点，当前节点就是这个节点的父节点；
-遇到 tag end 就出栈一个节点（还可以检查是否匹配）。

具体的过程可参考[词法解析和语法解析模拟过程](https://github.com/aimergenge/toy-html-parser)


### 渲染-合成-绘制
渲染过程把元素变成位图，合成把一部分位图变成合成层，最终的绘制过程把合成层显示到屏幕上。

 - 渲染
元素变成位图的过程。这里的位图就是在内存里建立一张二维表格，把一张图片的每个像素对应的颜色保存进去（位图信息也是 DOM 树中占据浏览器内存最多的信息，我们在做内存占用优化时，主要就是考虑这一部分）。

浏览器中渲染这个过程，就是把每一个元素对应的盒变成位图。这里的元素包括 HTML 元素和伪元素，一个元素可能对应多个盒（比如 inline 元素，可能会分成多行）。每一个盒对应着一张位图。 

- 合成
渲染过程不会把子元素渲染到位图上面，合成的过程，就是为一些元素创建一个“合成后的位图”（我们把它称为合成层），把一部分子元素渲染到合成的位图上面。
这就是我们要讲的合成的策略。我们前面讲了，合成是一个性能考量，那么合成的目标就是提高性能，根据这个目标，我们建立的原则就是最大限度减少绘制次数原则。

目前，主流浏览器一般根据 position、transform 等属性来决定合成策略，来“猜测”这些元素未来可能发生变化。

