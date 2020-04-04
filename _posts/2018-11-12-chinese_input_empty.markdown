---
layout:     post
title:      "关于ios下中文输入法 连续输入空格问题"
subtitle:   " \"continuously input problems \""
date:       2018-11-12 16:16:30
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
     
---

h5移动端页面 ，在iOS下中文输入法长输入的情况，会将英文输入，并且中间有空格。

![](https://img-blog.csdnimg.cn/20181112160901925.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNjg1MjIzNQ==,size_16,color_FFFFFF,t_70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")​

这个空格看上去跟普通的空格没什么区别，其实不然。通过string.charAt 方法 log出它的编码是8198，普通空格的编码是32.

解决初衷：将未处理的字符串传到后台，后台无法识别，会带有？乱码出来

  

```
// conUpdate (flag,默认为false)
if (this.checkChinese) { // 是否需要汉字校验
            let inputName = document.getElementById(this.myid)
            // compositionstart 监控开始输入汉字
            //  监控汉字输入
            inputName.addEventListener('compositionupdate', function () {
                _this.conUpdate = true
            })
            // 监控汉字输入结束
            inputName.addEventListener('compositionend', function () {
                _this.conUpdate = false

            })
            // 当前input框blur
            inputName.addEventListener('blur', function () {
                if (_this.value) {
                    _this.conUpdate = false
                    _this.checkedValue(_this.value)
                }
            })
        }


  //  监控到中文输入结束
            if (!this.conUpdate) { 
                // 根据charcode 作为分隔符  用‘’ 做连接  处理字符串
                let code = String.fromCharCode(8198)
                let arrStr = formValue.split(code)
                formValue = arrStr.join('')
            }
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "Click and drag to move")

复合事件  
复合事件(composition event)是DOM3级事件中新添加的一类事件,用于处理IME的输入序列。IME(Input Method Editor,输入法编辑器)可以让用户输入在物理键盘上找不到的字符。复合事件就是针对检测和处理这种输入而设计的。  
(1)compositionstart:在IME的文本复合系统打开时触发,表示要开始输入了。  
(2)compositionupdate:在向输入字段中插入新字符时触发。  
(3)compositionend:在IME的文本复合系统关闭时触发,表示返回正常键盘的输入状态。