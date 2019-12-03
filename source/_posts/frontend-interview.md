---
title: 前端面试题
date: 2019-12-03 09:06:07
tags:
  - JavaScript
  - CSS
  - HTML
  - 面试
category: 开发
---

* 下面的代码会输出什么
```javascript
var a = 10;
function A(){
    console.log(a);
    var a = 20;
    console.log(a);
    for(var a=1;a<5;a++){
    	setTimeout(function(){
    		console.log(a);
    	},0)
    }
}
A();
console.log(a);
```

* 注释处的this指向
```javascript
var obj = {
    showFunction:function(){
    }
}
obj.showFunction()  //this指向谁
var newObj = obj.showFunction;
newObj()           //this指向谁
```