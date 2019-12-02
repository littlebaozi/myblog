---
title: JavaScript基础之this
tags: JavaScript
category: 开发
date: 2019-12-02 15:16:21
---


this指向是在运行时决定的。

## 运行环境
浏览器环境下，最外层`this`指向`window`。如果是`strict`模式，指向`undefined`。Node环境中，最外层`this`也指向`undefined`。
```javascript
console.log(this) // [Object Window]
```

## 函数
1. 普通函数
```javascript
// 普通函数
function test() {
  console.log(this) // 1, Window
}
test()
```

2. 箭头函数
```javascript
const test = () => {
  console.log( this) // 1, Window
}
test()
```

3. 构造函数
```javascript
function test() {
  console.log(this) // test
}
new test()
```

## 定时器
定时器中函数的this指向一般是`window`。
```javascript
var a = 2
var obj = {
  a: 1,
  func: function () {
    console.log(this.a)
  },
  funcTimer: function () {
    setTimeout(function () {
      console.log(this.a)
    }, 0)
  }
}
obj.func() // 1
obj.funcTimer() // 2
```

## 对象中的this
* 对象中，普通函数指向对象。
```javascript
var a = 1
var obj = {
  a: 2,
  getA: function () {
    console.log(this.a)
  }
}
obj.getA() // 2

var getA = obj.getA
getA() // 1
```

* 箭头函数的话，指向的是`window`
```javascript
var a = 1
var obj = {
  a: 2,
  getA: () => {
    console.log(this.a)
  }
}
obj.getA() // 1
```

## 改变this指向
bind、apply、call
```javascript

```

## 问题解决
### 如何解决定时器中的this问题
1. 利用闭包
```javascript
var a = 2
var obj = {
  a: 1,
  funcTimer: function () {
    var that = this
    setTimeout(function () {
      console.log(that.a)
    }, 0)
  }
}
obj.funcTimer() // 1
```
或者
```javascript
var a = 2
var obj = {
  a: 1,
  funcTimer: function () {
    (function (that) {
      setTimeout(function () {
        console.log(that.a)
      }, 0)
    })(this)
  }
}
obj.funcTimer()
```

2. 使用bind
```javascript
var a = 2
var obj = {
  a: 1,
  funcTimer: function () {
    setTimeout(function () {
      console.log(this.a)
    }.bind(this), 0)
  }
}
obj.funcTimer() // 1
```

3. 箭头函数
```javascript
var a = 2
var obj = {
  a: 1,
  funcTimer: function () {
    setTimeout(() => {
      console.log(this.a)
    }, 0)
  }
}
obj.funcTimer() // 1
```