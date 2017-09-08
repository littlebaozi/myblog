---
title: js中的一些记录
date: 2017-08-02 15:25:25
tags: JavaScript
categories: JavaScript
---

## bind
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

## for...of 与 for...in
`for...in` 遍历key；`for...of` 遍历value
```javascript
let arr = ["a","b"];
for (a in arr) {
    console.log(a);//1,2
}

for (a of arr) {
    console.log(a);//a,b
}
```