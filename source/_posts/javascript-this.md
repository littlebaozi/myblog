---
title: JavaScript基础之this
tags: JavaScript
category: 开发
toc: true
---

## this的绑定方式
this指向是在运行时决定的。this的绑定方式可以分为默认绑定、隐式绑定、显式绑定、new绑定。

绑定的优先级：new > 显式 > 隐式 > 默认。

{% asset_img this.jpg this判断流程 %}

<!-- more -->

## 默认绑定
严格模式下，是`undefined`；否则是全局对象。
```javascript
// 普通函数
function test() {
  console.log(this);
}
test()
```

## 隐式绑定
对象中，普通函数指向对象。
```javascript
var a = 1;
var obj = {
  a: 2,
  getA: function () {
    console.log(this.a);
  }
}
obj.getA() // 2

var getA = obj.getA;
getA(); // 1
```

## 显式绑定
使用bind、apply、call可以改变this的指向。
```javascript
var a = 1;
var obj = {
  a: 2
}

function func() {
  console.log(this.a);
}

func(); // 1
func.bind(obj)(); // 2
func.call(obj); // 2
func.apply(obj); // 2
```

但是，这对箭头函数是无效的
```javascript
var a = 1;
var obj = {
  a: 2
}

const func = () => {
  console.log(this.a);
}

func(); // 1
func.bind(obj)(); // 1
func.call(obj); // 1
func.apply(obj); // 1
```

* `forEach`、`map`、`every`、`filter`、`find`、`findIndex`可以传第二个参数指定回调函数中的this绑定
```javascript
var obj = {
  a: 'a'
};

[1, 2, 3].forEach(function(item) {
  console.log(item, this.a) // 1 a/2 a/3 a
}, obj);

var res = [1, 2, 3].map(function(item) {
  return this.a;
}, obj);
console.log(res); // ['a','a','a']
```

## new绑定
* 构造函数
构造函数中的this在实例化后就绑定且不可更改了
```javascript
var a = 1;
function test() {
  this.a = 2
  this.getA = function () {
    console.log(this.a);
  }
  console.log(this.a); // 2, test
}
var t = new test();
a = 3;
t.getA(); // getA是可以通过bind、call、apply更改this指向
```

## 例外
* 箭头函数
箭头函数中没有this，this是继承自上一层运行环境的this
```javascript
var a = 1;
var obj = {
  a: 2,
  getA: () => {
    console.log(this.a);
  }
}
obj.getA(); // 1
var ga = obj.getA;
ga(); // 1
```

```javascript
var a = 1;
var obj = {
  a: 2,
  getA: function () {
   var fn = () => {
    console.log(this.a);
    }
   fn()
  }
}
obj.getA(); // 2
var ga = obj.getA
ga() // 1
```

* 定时器
定时器中函数的this指向一般是`window`。
```javascript
var a = 2;
var obj = {
  a: 1,
  func: function () {
    console.log(this.a);
  },
  funcTimer: function () {
    setTimeout(function () {
      console.log(this.a);
    }, 0);
  }
}
obj.func(); // 1
obj.funcTimer(); // 2
```

## 问题解决
### 如何解决定时器中的this问题
1. 利用闭包
```javascript
var a = 1;
var obj = {
  a: 2,
  funcTimer: function () {
    var that = this;
    setTimeout(function () {
      console.log(that.a);
    }, 0);
  }
}
obj.funcTimer(); // 2
```
或者
```javascript
var a = 1;
var obj = {
  a: 2,
  funcTimer: function () {
    (function (that) {
      setTimeout(function () {
        console.log(that.a);
      }, 0);
    })(this);
  }
}
obj.funcTimer(); // 2
```

2. 使用bind
```javascript
var a = 1;
var obj = {
  a: 2,
  funcTimer: function () {
    setTimeout(function () {
      console.log(this.a);
    }.bind(this), 0);
  }
}
obj.funcTimer(); // 2
```

3. 箭头函数
```javascript
var a = 1;
var obj = {
  a: 2,
  funcTimer: function () {
    setTimeout(() => {
      console.log(this.a);
    }, 0);
  }
}
obj.funcTimer(); // 2
```

## 试题
* 下面this指向
```javascript
var obj = {
    showFunction:function(){
    }
}
obj.showFunction()  //this指向谁
var newObj = obj.showFunction;
newObj()           //this指向谁
```

* 实现bind、call、apply