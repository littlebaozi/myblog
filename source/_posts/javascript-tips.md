---
title: js中的一些记录
date: 2017-08-02 15:25:25
tags: JavaScript
categories: JavaScript
---

## 一、对象
### 1. bind
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

## 二、数组
### 1. for...of 与 for...in
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

### 2. 扁平化合并数组
```javascript
Array.prototype.concat.apply(["a","b"], ["c","d"])
Array.prototype.concat.apply([], [["a","b"],["c", "d"]])
```

### 3. 转换数组对象
`[].slice.call(arguments)`，可以在函数内将参数转换成数组

### 3. 求最大值
```javascript
Math.max.apply(null, [10, -1, 5])
```

## 时间
### 1. 时间戳
```javascript
// 方法
Date.now()

// 对象方法
new Date().getTime()

// 对象和操作符
+new Date()
```