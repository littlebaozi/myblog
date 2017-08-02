---
title: js中的this问题
date: 2017-08-02 15:25:25
tags: JavaScript
categories: JavaScript
---

普通函数中this在非严格模式下指向window，在严格模式下是undefined
```javascript
'use strict'
function func(){
    console.log(this)
}
```

通过call，bind可以改变this的指向
```javascript
var str = 'abc';

var Obj = {
    str: 'cde',
    getStr: function(){
        console.log(this.str)
    }
}
Obj.getStr();
Obj.getStr.call();
Obj.getStr.bind(this)();
```
