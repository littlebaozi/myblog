---
title: 使用babel转ES6
date: 2017-08-10 11:40:29
thumbnail: http://babeljs.cn/images/logo.png
tags: 
- babel
- es6
categories: 工具
---

## 一、babel安装
### 1. 全局安装
```bash
npm install -g babel-cli
```

使用babel命令编译文件：

```
babel src/index.js -o dist/index.js
```

<!--more-->

### 2. 本地安装
```
npm install --save-dev babel-cli
```

使用npm命令编译：
修改package.json，添加命令
```json
{
    "scripts:{
        "build": "babel src/index.js -o dist/index.js"
    }
}
```

运行`npm run build`就可以编译了

## 二、ES6语法转ES5
1. 先要安装相应插件
```
npm install --save-dev babel-preset-es2015
```

2. 在目录下新建.babelrc文件，填写配置
```json
{
    "presets":[
        "es2015"
    ]
}
```

## 三、ES6 API转ES5
如果只用`babel-preset-es2015`，babel只会转换let、const这些语法，像Array.from()、String.includes()、Promise等新API不会被转码。
所以，需要安装`babel-polyfil`。
```bash
npm install -save-dev babel-polyfill
```