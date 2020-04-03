---
layout:     post
title:      "谈谈页面渲染机制"
subtitle:   " \"谈谈页面渲染机制\""
date:       2018-04-21 12:00:00
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---

 坐了很久不知道怎么开始，那就把之前看的面试视频（慕课网：前端面试必备技巧）里的知识点盘点下当作一种输出，加强下理解和记忆，老师讲的很好，所以我也要努力呀。以前看的多忘的也多，实践相对较少，总结性的就是更少了。“纸上得来终觉浅，绝知此事要躬行”,实践输出才是检验真理的唯一标准。好了，废话不多说，开始吧。

1.了解doctype以及它的作用

标准的html页面头部第一行都有doctype的声明，并且你会发现并不是所有的doctype都一致,它是用来干嘛的，又有哪几种呢？

doctype是什么：



DTD:(document type definition,文档类型定义) 是一系列的语法规则，用来定义xml或者(x)HTML的文件类型。就是告诉浏览器当前文档是什么类型的，需要用什么引擎来解析它。

DOCTYPE是document type(文档类型)的简写，用来说明你用的XHTML或者HTML是什么版本。浏览器就根据你定义的DTD来解释你页面的标识，并展现出来。

常见的DOCTYPE类型：



1.html5

<!DOCTYPE html>



1.用于XHTML 4.0 的严格型 （该dtd包含所有的HTML元素和属性，但不包括展示型的和弃用性的元素比如font)

<!DOCTYPE HTMLPUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

/用于XHTML 4.0 的传统型 （该dtd包含所有的HTML元素和属性，包括展示型的和弃用性的元素比如font)

<!DOCTYPE HTMLPUBLIC "-//W3C//DTD HTML 4.01Transitional//EN " "http://www.w3.org/TR/html4/loose.dtd">
HTML5因为不基于 SGML，因此不需要对DTD进行引用;但是需要doctype来规范浏览器的行为(让浏览器按照它们应该的方式来运行)。 而HTML4.01基于SGML,所以需要对DTD进行引用，才能告知浏览器文档所使用的文档类型。

2.浏览器渲染过程

首先放一张浏览器渲染流程图（网络上找的）

![浏览器渲染流程图](https://img-blog.csdn.net/20180421114818971?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


当浏览器拿到 HTML 和css，通过HTML解析器和css解析器分别生成DOM tree 和 CSSom两者结合形成renderTree ,此时的renderTree还不知道在哪个位置放置节点，是通过浏览器的Layout告诉它具体的位置样式，最后绘制出我们最终看到的页面效果。这个就是浏览渲染的基本过程。

说到渲染，有两个概念需要提及，重绘和重排。那什么是重绘和重排，什么操作会触发这个两个过程呢？

重排Reflow：DOM结构中的各个元素都有自己的盒子模型，浏览器根据各种样式计算得出结果将元素放到具体的位置上，这个过程我们称之为reflow

哪些动作触发reflow



增删改DOM节点
修改css样式
移动DOM节点
改变窗口大小，改变文字大小
内容的改变，
设置offsetHeight,offsetWidth
重绘Repaint:浏览器根据盒子具体的位置，大小和颜色等属性，将各元素按照各自的特性都绘制一遍。这个过程为重绘。如果只是改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性，将只会引起浏览器 repaint

哪些动作触发repaint: css和dom的改动都会引起repaint

浏览器在渲染过程中的reflow和repaint会大大影响web的性能,因此我们要避免reflow和减少repaint,如减少上面的触发条件;在为dom添加节点的时候，创建一个变量保存所有的子节点，当所有操作完毕之后 再一次性添加到dom上来。

以上。

写文档真比想象当中要困难，望继续坚持。
















