---
title: JavaScript技巧记录
date: 2017-08-02 15:25:25
tags: JavaScript
categories: JavaScript
---

## 对象
* bind
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

## 数组
* for...of 与 for...in
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

* 初始化元素相同的数组
`Array.from({length: 5}, ()=>10)`初始化长度5，元素值都为10的数组

* 扁平化合并数组
```javascript
Array.prototype.concat.apply(["a","b"], ["c","d"])
Array.prototype.concat.apply([], [["a","b"],["c", "d"]])
```

* 转换数组对象
`[].slice.call(arguments)`，可以在函数内将参数转换成数组

* 求最大值/小值
```javascript
Math.max.apply(null, [10, -1, 5]);
Math.max(...[1,3,2]);
Math.min.apply(null, [10, -1, 5]);
Math.min(...[1,3,2]);
```

## 时间
* 时间戳
```javascript
// 方法
Date.now()

// 对象方法
new Date().getTime()

// 对象和操作符
+new Date()
```

## 计算
* 浮点计算 `0.1+0.2=0.30000000000000004` 先*10转换成整数计算加减再/10转换为小数

## 存储
* localstorage 存储`JSON.stringfy()` 转换成字符串；取出 `JSON.parse()`

## bom
* Iframe刷新
```javascript
tabPane.contentWindow.location.href = ""; // 会改变src
tabPane.contentWindow.location.reload();  // 重新设置src，仍然带原来的参数请求；无法重新设置search
```
