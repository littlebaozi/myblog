---
title: 奇舞特训营 JavaScript （第 3 课）变量、值与类型
date: 2017-06-19 21:32:51
thumbnail: https://t.75team.com/static/img/badges.png
tags: 
- JavaScript
categories: 
- JavaScript
- 奇舞特训营
---

# 变量值、类型

## 一、JS类型说明
* 动态类型+弱类型的语言
* 变量、属性在运行期决定类型
* 存在隐式类型转换
* 有一系列识别类型的反射方法

## typeof
```javascript
typeof undefined === 'undefined'
typeof null === 'object'

typeof true === 'boolean'
typeof 123 === 'number'
typeof 'username' === 'string'
typeof {username:'jack'} === 'object'
typeof ['jack'] === 'object'  //数组也是object，[] instanceof Array == true
typeof Symbol() === 'symbol'

typeof function(){} === 'function'
```
<!--more-->

## 二、变量声明
* var - 声明一个变量，可选择将其初始为一个值
* let - 声明一个块级作用域变量，可选择选择将其初始为一个值
* const - 声明一个只读的常量

### 1. 使用var的注意事项
* var不支持块级作用域
* var存在变量提升（Variable hoising）

```javascript
console.log(a === undefined);

var a = 10; 

//上面这部分相当于
/*
var a;
console.log(a === undefined);
a = 10;
*/

function foo(){

  try{
     console.log([a,i]);  //此时a在foo内var定义了，但还未赋值，所以是undefined
  }catch(e){
    
  }
 
  
  var a = 20;
  
  for(var i = 0; i < a; i++){
    
  }
  
  console.log([a, i]);
  
}

foo();

/*output
    true
    [undefined, undefined]
    [20, 20]
*/
```

<a class="jsbin-embed" href="http://jsbin.com/tuhobaz/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.3"></script>

### 2. 使用let的注意事项
* 块级作用域
* 同一作用域中不允许重复声明
* 暂存死区（Temporal dead zone）
* 循环中的let作用域
* 浏览器兼容性

<a class="jsbin-embed" href="http://jsbin.com/nuhesa/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.3"></script>

#### 2.1 暂存死区
<a class="jsbin-embed" href="http://jsbin.com/zurulo/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.5"></script>

#### 2.2 循环(let的词法作用域)
```javascript
// onclick是异步事件，所有按钮点击都输出第五个按钮。
var buttons = document.querySelectorAll('button');

for(var i = 0; i < buttons.length; i++){
  buttons[i].onclick = 
    evt => console.log('点击了第' + i + '个按钮');
}
```
　　解决方法：
1. 闭包
```javascript
var buttons = document.querySelectorAll('button');

for(var i = 0; i < buttons.length; i++){
  (function(i){
    buttons[i].onclick = 
    evt => console.log('点击了第' + i + '个按钮');
  })(i);
}
```

<a class="jsbin-embed" href="http://jsbin.com/zimovum/embed?html,js,console,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.5"></script>

2. let
```javascript
var buttons = document.querySelectorAll('button');

for(let i = 0; i < buttons.length; i++){
  buttons[i].onclick = 
    evt => console.log('点击了第' + i + '个按钮');
}
```

<a class="jsbin-embed" href="http://jsbin.com/zuzevok/embed?html,js,console,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.5"></script>

### 4. 使用const的注意事项
* const声明的标识符所绑定的值不可以再次改变
* ES6 const 的其他行为和let一样

```javascript
// 不能修改引用，但是能修改属性
const A = {a:1};
A.a = 2;
console.log(A)

// 使用freeze禁止修改
const B = Object.freeze({a:1});
B.a = 2;
console.log(B);
```
<a class="jsbin-embed" href="http://jsbin.com/foxevi/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.5"></script>

## 三、不声明直接使用

```javascript
// 实际上y是没有声明的，在严格模式下会报错
(function(){
  'use strict';
  
  var x = y = 0;
})();

console.log([typeof x, typeof y]) //非严格模式下，输出[undefined, number]，因为y是全局的
```

```javascript
(function f(j = 0){
  //'use strict'; es6下不能在这里使用严格模式
  
  var x = y = 0;
})();
```

## 四、抽象相等
* 严格相等与非严格相等
* null == undefined（非严格比较）, null != undefined
* 非严格相等的类型转换
* NaN与任何值（包括自身）都不相等

```javascript
null === undefined //false
null == undefined //true

'1' == 1 //true
[] == 0 //true
'' == 0 //true
0 == false //true
1 == true //true

NaN == NaN //false
```

```javascript
var a = [];
if(a){
  // if中不会强制转换，是true
  console.log('a');
}
console.log(!![],  [] == true); // false, false
```
<a class="jsbin-embed" href="http://jsbin.com/dejife/1/embed?js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.6"></script>

## 五、Boolean类型
* true 和 false
* 0、''、null、undefined被隐式转换为false，其他转为true
* 建议采用严格比较，可以通过`!!`将非boolean值转为boolean
* 布尔操作符`&&`和`||`并不会转换类型
* `&&`和`||`的短路特性
* 比较操作符总是返回boolean类型

<a class="jsbin-embed" href="http://jsbin.com/seqilup/embed?js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.41.6"></script>

## 六、Number类型
1. ### 数值范围：
  * 整数 -2^53 ~ 2^53
  * 小数精度 Number.EPSILON
  * Infinity、Number.MAX_VALUE、Number.MIN_VALUE

  
```javascript
console.log([Number.MAX_SAFE_INTEGER, Number.MIN_SAFE_INTEGER]); //[9007199254740991, -9007199254740991]

var bigInteger = Number.MAX_SAFE_INTEGER + 1;

console.log([bigInteger, bigInteger + 1, bigInteger + 2]); //[9007199254740992, 9007199254740992, 9007199254740994]
```

<a class="jsbin-embed" href="//code.h5jun.com/wir/2/embed?html,css,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

### 2. 浮点数进度问题
限制精度方法：`toPrecision()` `toFixed()`
```javascript
console.log(0.2 + 0.4); //0.6000000000000001 IEEE754
```
<a class="jsbin-embed" href="//code.h5jun.com/hew/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

### 3. 二进制、八进制、十进制、十六进制
严格模式`0o100`、非严格模式`0100`
<a class="jsbin-embed" href="//code.h5jun.com/bus/1/embed?html,css,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>
* +0 和 -0

## 七、String
1. 引号规范
2. 转义字符
<a class="jsbin-embed" href="//code.h5jun.com/dayi/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

3. 字符串与字符
`Array.from(str)` == `str.split('')`
<a class="jsbin-embed" href="//code.h5jun.com/qud/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

4. 字符串与类型转换
```javascript
'1'-0+2 //用这种方法加
Number('0b100aaa') // 有字符串会返回NaN
```
<a class="jsbin-embed" href="//code.h5jun.com/pey/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

5. 常用字符串操作
```javascript
['abc','def'].join('')  //用+有些浏览器性能不好

// 逆序
str.split('').reverse().join(''); //先转换数组

str.slice(2,3);  //起始位置、结束位置
str.substr(2,3);  //起始位置、长度

```

<a class="jsbin-embed" href="//code.h5jun.com/caga/3/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

6. 模板字符串（ES6）

<a class="jsbin-embed" href="//code.h5jun.com/nis/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

模板可以嵌套，使用map拼接模板
```javascript

let data = [
  {name: 'Akira', age: 35},
  {name: 'Jane',  age: 26},
  {name: 'Jhon',  age: 54}
];

let formatedList = (data) => `
<ul>
  ${
    data.map(item => `
      <li><span>姓名：${item.name}</span><span>年龄：${item.age}</span></li>
    `).join('')
  }
</ul>
`;
```

<a class="jsbin-embed" href="//code.h5jun.com/jor/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

## 八、Object

### 1. 对象的属性
* 属性名规则：可以是有效字符串或者任意可转换为有效字符串的类型
* 属性的访问和遍历

#### 1.1 对象创建
* `new Object()`
* `let obj = {}`
* `Object.create()`

<a class="jsbin-embed" href="//code.h5jun.com/jug/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

#### 1.2 属性的访问

* []属性访问的好处是可以计算
* ES6 中字面量的 key 也支持属性计算

<a class="jsbin-embed" href="//code.h5jun.com/yam/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

#### 1.3 属性的遍历
* `for...in`
* `Object.keys`

<a class="jsbin-embed" href="//code.h5jun.com/nef/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

### 2. 值和引用
#### 2.1 值类型与引用类型
对象是引用类型，在函数中修改后，会改变值。
<a class="jsbin-embed" href="//code.h5jun.com/vav/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

#### 2.2 基本类型对应的包装类型
包装类型没什么卵用

```javascript
var str = "Hello World";
var strObj = new String(str);

console.log([str, strObj]);  
console.log([typeof str, typeof strObj]);   //["string", "object"]
console.log([str instanceof String, strObj instanceof String]);  //[false, true]

var n = new Number(10);
console.log([typeof n, typeof ++n]); //["object", "number"]
```

<a class="jsbin-embed" href="//code.h5jun.com/hud/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

#### 2.3 对象的拷贝
1. ES5浅拷贝
  `Object.assign()`。但是二级属性是对象的仍然是引用类型
2. 深拷贝

<a class="jsbin-embed" href="//code.h5jun.com/til/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

### 3. 类型与构造器
* new和constructor
* 3.2 prototype
* 3.3 instanceOf
* 3.4 ES6 class

#### 3.1 对象的构造器

<a class="jsbin-embed" href="//code.h5jun.com/ruf/2/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

#### 3.2 原型链
原型链有优先级。
<a class="jsbin-embed" href="//code.h5jun.com/fen/2/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

<b>问题：额外的构造器调用（未理解）</b>
<a class="jsbin-embed" href="//code.h5jun.com/tud/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

* 解决方法1：通用化父类构造器
<a class="jsbin-embed" href="//code.h5jun.com/dak/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

* 解决方法2：延迟父类构造器调用
<a class="jsbin-embed" href="//code.h5jun.com/geq/2/embed?html,js,output">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

### 4. 内置类型
所有的类型的prototype都指向Object
* ✅ Object
* ✅ Function
* ✅ Array
* Date
* Regex
* Error
* Math​​​​​​​
* ArrayBuffer
* ✅ Promise
* DataView
* Map
* Set
* TypedArray
​​​​​​​* Proxy

### 5. 对象的高级属性
