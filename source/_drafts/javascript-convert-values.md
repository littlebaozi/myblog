---
title: Javascript类型转换
tags: JavaScript
category: 开发
---

JavaScript类型转换为三种：
* 转为布尔值
* 转为字符串
* 转为数字

对象强制转换，调用的是`toPrimitive`。`valueOf`、`toString`

## 面试题
```javascript
if([]==false){console.log(1)};
if({}==false){console.log(2)};
if([]){console.log(3)}
if([1]==[1]){console.log(4)}
```

