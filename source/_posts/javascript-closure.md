---
title: JavaScript基础之闭包
date: 2019-12-03 09:25:08
tags: JavaScript
category: 开发
---

## 什么是闭包
闭包指的是能够访问另一个函数作用域的函数。

```javascript
function funcA() {
  var val = 1;

  return function () {
    console.log(val)
  }
}
var funcB = funcA(); // 运行函数后，val的值仍然保留着
funcB();
```

## 闭包的应用
```javascript
// 下面的代码中，打印的i都是5
for(var i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log(i)
  }, 1000)
}
```

解决方法
```javascript
// 利用闭包
for(var i = 0; i < 5; i++) {
  (function(j){
    setTimeout(function () {
      console.log(j)
    }, 1000)
  })(i)
}

// 利用let
for(let i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log(i)
  }, 1000)
}

// 利用参数
```

## 典型题