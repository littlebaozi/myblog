---
title: JavaScript基础之this
tags: JavaScript
category: 开发
---

## 运行环境
浏览器环境下，最外层`this`指向`window`。如果是`strict`模式，指向`undefined`。Node环境中，最外层`this`也指向`undefined`。
```javascript
console.log(this) // window
```

## 函数中的this

### 普通函数
```javascript
function test() {
  console.log(this)
}

test() // window
```

### 箭头函数
```javascript
const test = () => console.log(this)
test(); // window
```

### 定时器


## 对象中的this

## 改变this
bind、apply、call