---
layout:     post
title:      "最近新get到的vue新功能"
subtitle:   " \"最近新get到的vue新功能\""
date:      2020-06-01 20:55:12 
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---
## 导言
用vue 蛮久了才发现自己有很多比较方便的用法都没有用过，还是自己当初在开始学的时候掌握的，是在惭愧。这些在各种组件源码中经常看到，以为都是很高级很高级的用法，其实你会发现这些都是在Vue官方文档中写的很仔细很仔细的，但是我自己从来没有看过。这次算给自己上了一节课了，在刚开始学习的时候可以粗略的过，但是用了一段时间之后，一定要回过来在仔细的阅读官方文档。
 
## js部分

### 组件递归
> 组件是可以在它们自己的模板中调用自身的。不过它们只能通过 `name` 选项来做这件事：


```javascript
// 一般编写复杂组件的时候可能会用到，平常业务中可能不太用上，先记下
name: 'unique-name-of-my-component'
```


### sync

```javascript
// 父组件
<message :show.sync="show">添加成功</message>
// 子组件
<div  class="message-box"  v-if="show">
<slot>消息成功了。。。</slot>
<span  class="message-box-close" @click="$emit('update:show', false)">X</span>
</div>
```
### 自定义组件的v-model
> 允许一个自定义组件在使用 v-model 时定制 prop 和 event。默认情况下，一个组件上的 v-model 会把 value 用作 prop 且把 input 用作 event，但是一些输入类型比如单选框和复选框按钮可能想使用 value prop 来达到不同的目的。使用 model 选项可以回避这些情况产生的冲突。
 -  普通value ,prop用法。自定义组件的时候使用v-model而不是在父级上再定义 v-if
``` javascript
// 父组件
<test-com :title="title"  v-model="show" />

// 子组件
<template>
  <div>
    <div v-if="isShow">{{title }}
      <p>我是来测试了</p>
    </div>
    <div>input 测试</div>
  </div>
</template>
<script>export
default {
    data() {
      return {
        telephone:
        undefined,
        isShow: this.value,
        telReg: undefined
      };
    },
    watch: {
      value(val) {
        console.log("val", val);
        this.isShow = val;
      },
      isShow(val) {
        this.$emit("input", val);
      }
    },
    props: {
      title: {
        type: String,
        default:""
      },
      value: {
      default:
        true,
        type: Boolean
      }
    }
  };</script>

```
 -  自定义model

 ```javascript
 // 父组件
 <base-checkbox  v-model="loginvue" @change="customizeChange" />
 // 子组件  base-check.vue
 <template>
  <div>
    <input type="checkbox" v-bind:checked="checked" v-on:change="$emit('change', $event.target.checked)" /></div>
</template>
<script>export
default {
    name:
    "base-checkbox",
    model: {
      prop: "checked",
      event: "change"
    },
    props: { // 这个地方别忘记定义喽
      checked: Boolean
    }
  };</script>
 ```


### 监听组件生命周期

> 实际开发过程中会遇到当子组件某个生命周期完成之后通知父组件，然后在父组件做对应的处理

```javascript
// 传统方式
// 子组件在对应的钩子中发布事件  
created(){  
this.$emit('done')  
}  

// 父组件订阅其方发  
<list @done="childDone">
 // 使用 通过`@hook`监听子组件的生命周期
<list @hook:mounted="listMounted" />
```

###  路由组件传参
```javascript
{
    path: 'notice/:tab/:id',
    name: 'user-notice',
    component: UserNotice,
    props: true, //  `route.params` 将会被设置为组件属性。
}
// 组件中
 props: ["id", "tab"],
```
更多参考：[https://router.vuejs.org/zh/guide/essentials/passing-props.html](https://router.vuejs.org/zh/guide/essentials/passing-props.html)

### 程序化事件监听器
>  通常页面中有定时器的时候，我们会定义一个timer变量，在页面销毁之前清除定时器。`this.timer` 唯一的作用只是为了能够在 `beforeDestroy` 内取到计时器序号，除此之外没有任何用处。

```javascript
export default {  
mounted() {  
this.timer = setInterval(() => {  
console.log(Date.now())  
}, 1000)  
},  
beforeDestroy() {  
clearInterval(this.timer)  
}  
}
```

> 如果可以的话最好只有生命周期钩子可以访问到它。这并不算严重的问题，但是它可以被视为杂物。

>我们可以通过 `$on` 或 `$once` 监听页面生命周期销毁来解决这个问题：

```javascript
export default {  
mounted() {  
this.creatInterval('hello')  
this.creatInterval('world')  
},  
creatInterval(msg) {  
let timer = setInterval(() => {  
console.log(msg)  
}, 1000)  
this.$once('hook:beforeDestroy', function() {  
clearInterval(timer)  
})  
}  
}
```

###  全局挂载组件

-  Message  提示弹框

```javascript

// Message/index.js
import Vue from "vue";
import message from './message';
const MessageConstructor = Vue.extend(message);
const messageInstance = new MessageConstructor();

messageInstance.$mount();
document.body.appendChild(messageInstance.$el)

const Message = (option = {}) = >{
    messageInstance.add(option);
}
export default Message;

```

```javascript
// message/message.vue
<template>
  <div class="wrap">
    <div class="message" :class="item.type" v-for="item in notices" :key="item._name">
      <div class="content">{{item.content}}</div></div>
  </div>
</template>
<script>// 默认选项
  const DefaultOptions = {
    duration: 1500,
    type: "info",
    content: "这是一条提示信息！"
  };
  let mid = 0;
  export
default {
    data() {
      return {
        notices:
        [],
        isShow: false
      };
    },
    methods: {
      add(notice = {}) {
        this.isShow = true;
        // name标识 用于移除弹窗
        let _name = this.getName();
        // 合并选项
        notice = Object.assign({
          _name
        },
        DefaultOptions, notice);
        this.notices.push(notice);
        setTimeout(() = >{
          this.removeNotice(_name);
        },
        notice.duration);
      },
      getName() {
        return "msg_" + mid++;
      },
      removeNotice(_name) {
        this.isShow = false;
        let index = this.notices.findIndex(item = >item._name === _name);
        this.notices.splice(index, 1);
      }
    }
  };</script>

```
 -   confirm 弹框，方法类写在inde.js中，通过实例创建。

 ```javascript
 // comfrim/index.js
 import confirm from './confirm';
import Vue from 'vue';
import {
    pageScroll
}
from '@/utils/assist';

// 构造子类
let confirmConstructor = Vue.extend(confirm);
// 实例化组件
let confirmInstance = new confirmConstructor({
    el: document.createElement('div')
});
// 也可通过此方法
// $mount可以传入选择器字符串，表示挂载到该选择器
// 如果不传入选择器，将渲染为文档之外的的元素，你可以想象成 document.createElement()在内存中生成dom
// confirmInstance.$el获取的是dom元素
// confirmInstance.$mount();
const hashChange = function() {
    pageScroll.unlock();
    const el = confirmInstance.$el;
    el.parentNode && el.parentNode.removeChild(el);
}
confirmConstructor.prototype.closeConfirm = function(stay, callback) {

    let stopClose = true;
    if (typeof callback === 'function') {
        stopClose = callback();
        if (stopClose === undefined) stopClose = true;
    }

    if (!stopClose || stay) return;
    pageScroll.unlock();

    const el = confirmInstance.$el;
    el.parentNode && el.parentNode.removeChild(el);
    window.removeEventListener("hashchange", hashChange);
}

const Confirm = (options = {}) = >{
    confirmInstance.mes = options.mes || '';
    confirmInstance.align = options.align || '';
    confirmInstance.title = options.title || '提示';
    confirmInstance.opts = options.opts;
    window.addEventListener("hashchange", hashChange);
    document.body.appendChild(confirmInstance.$el);
    pageScroll.lock();
}

export default Confirm;

```

```javascript
// confirm/confirm.vue
<template>
  <div class="wb-dialog-black-mask">
    <div class="wb-confirm">
      <div class="wb-confirm-hd">
        <strong class="wb-confirm-title" v-html="title"></strong>
      </div>
      <div class="wb-confirm-bd" :class="align ? 'text-'+align :'' " v-html="mes"></div>
      <template v-if="typeof opts == 'function'">
        <div class="wb-confirm-ft">
          <a href="javascript:;" class="wb-confirm-btn default" @click.stop="closeConfirm(false)">取消</a>
          <a href="javascript:;" class="wb-confirm-btn primary" @click.stop="closeConfirm(false, opts)">确定</a></div>
      </template>
      <template v-else>
        <div class="wb-confirm-ft">
          <a href="javascript:;" class="wb-confirm-btn" :key="key" v-for="(item, key) in opts" :class="typeof item.color == 'boolean' ? (item.color ? 'primary' : 'default') : ''" :style="{color: item.color}" @click.stop="closeConfirm(item.stay, item.callback)">{{item.txt}}</a></div>
      </template>
    </div>
  </div>
</template>
<script type="text/babel">export
default {
    props:
    {
      title:
      String,
      mes: String,
      align: String,
      opts: {
        type: [Array, Function],
      default:
        () = >{}
      }
    }
  };</script>

```

## css 部分

### 样式穿透
> 样式穿透在开发中修改第三方组件样式是很常见，但由于scoped属性的样式隔离，可能需要去除scoped或是另起一个style。这些做法都会带来副作用（组件样式污染、不够优雅），样式穿透在css预处理器中使用才生效。
```javascript
- less使用 / deep /

<style scoped lang = "less" > 
.content / deep / .el - button {
    height: 60px;
} < /style>

- scss使用 ::v-deep
<style scoped lang="scss">
.content ::v-deep .el-button {
  height: 60px;
}
</style >

- stylus使用 >>> 
<style scoped lang = "stylus" > 
外层 >>> .custon - components {
    height: 60px;
} < /style>

```

### css module
同scoped 一样起到隔绝作用域的功能。相比于scoped 它更能减少命名冲突,为每一个局部类赋予全局唯一的类名，这样组件样式间就不会相互影响了 .

```javascript
 
<template>
  <p :class="$style.red"> // => class="red_1nczEeyy"
    This should be red
  </p>
   <p :class="{ [$style.red]: isRed }"> Am I red?
    </p>
    <p :class="[$style.red, $style.bold]"> Red and bold </p>
</template>
 
<style module>
.red {
  color: red;
}
.bold {
  font-weight: bold;
}
</style>
 
// 你也可以通过 JavaScript 访问到它：
created(){
console.log(this.$style.red)
}

```

### svg loading

> 发现了一个类google的loading动画

```html
<svg class="circular" viewBox="25 25 50 50">
  <circle class="path" cx="50" cy="50" r="15" fill="none" stroke-width="2" stroke-miterlimit="10" /></svg>
```
```css
.myloader {
	position:relative;
	margin:0 auto;
	width:60px;
	height:100%;
}
.myloader:before {
	content:"";
	display:block;
	padding-top:100%;
}
.circular {
	animation:rotate 2s linear infinite;
	-webkit-animation:rotate 2s linear infinite;
	height:auto;
	transform-origin:center center;
	-webkit-transform-origin:center center;
	width:100%;
	position:absolute;
	top:0;
	bottom:0;
	left:0;
	right:0;
	margin:auto;
}
.path {
	stroke-dasharray:1,200;
	stroke-dashoffset:0;
	animation:dash 1.5s ease-in-out infinite,color 6s ease-in-out infinite;
	-webkit-animation:dash 1.5s ease-in-out infinite,color 6s ease-in-out infinite;
	stroke-linecap:round;
}
@keyframes rotate {
	100% {
	transform:rotate(360deg);
}
}@keyframes dash {
	0% {
	stroke-dasharray:1,200;
	stroke-dashoffset:0;
}
50% {
	stroke-dasharray:89,200;
	stroke-dashoffset:-35px;
}
100% {
	stroke-dasharray:89,200;
	stroke-dashoffset:-124px;
}
}@keyframes color {
	100%,0% {
	stroke:#d62d20;
}
40% {
	stroke:#0057e7;
}
66% {
	stroke:#008744;
}
80%,90% {
	stroke:#ffa700;
}
}@-webkit-keyframes rotate {
	100% {
	transform:rotate(360deg);
}
}@-webkit-keyframes dash {
	0% {
	stroke-dasharray:1,200;
	stroke-dashoffset:0;
}
50% {
	stroke-dasharray:89,200;
	stroke-dashoffset:-35px;
}
100% {
	stroke-dasharray:89,200;
	stroke-dashoffset:-150px;
}
}@-webkit-keyframes color {
	100%,0% {
	stroke:#d62d20;
}
40% {
	stroke:#0057e7;
}
66% {
	stroke:#008744;
}
80%,90% {
	stroke:#ffa700;
}
}
```

----
参考来源：
- [https://segmentfault.com/a/1190000007694540](https://segmentfault.com/a/1190000007694540)

- [https://mp.weixin.qq.com/s/U5a7WyHDKJJqqkF8zi-dcA](https://mp.weixin.qq.com/s/U5a7WyHDKJJqqkF8zi-dcA)
- [https://mp.weixin.qq.com/s/NYjm1xk9XqOqxu6LiviuoQ](https://mp.weixin.qq.com/s/NYjm1xk9XqOqxu6LiviuoQ)














