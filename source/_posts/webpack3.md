---
title: webpack3
date: 2017-10-17 16:28:37
tags:
- webpack
- 构建工具
categories: 
- 工具
---

## 准备工作
1. 全局安装webpack
`npm install -g webpack`

2. 项目初始化
`npm init`初始项目

3. 项目安装webpack
`npm install --save-dev webpack`

4. 新建项目目录
- dist 打包后文件夹
 - index.html
- src 开发文件夹
 - app.js

在`index.html`中引入`bundle.js`(webpack生成)。

## webpack.config.js
### 1. 新建`webpack.config.js`，写入最基本的内容：
```javascript
module.exports={
    //入口文件的配置项
    entry:{},
    //出口文件的配置项
    output:{},
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```
* entry是webpack打包的起点，可以有多个。
* output是webpack编译合并后输出的目录，只能是一个。
* module里可以写各种loader配置，分别处理不同模块（webpack 把每个文件(.css, .html, .scss, .jpg, etc.) 都作为模块处理），比如处理css的引入，图片转换压缩等。
* plugins可以使用各种插件，解决loader无法实现的操作

### 2. 入口/出口配置
先引入path，`const path = require('path');`
```javascript
entry: {
    app: './src/app.js' // app是可以自己命名的
},
output: {
    path: 'path:path.resolve(__dirname,'dist'),',  // 打包后的文件输出目录
    filename: 'bundle.js'  // 打包文件名
}
```

### 3. 运行服务器dev server
1. 先安装，`npm install --save-dev webpack-dev-server`
```javascript
// 基本的配置项
devServer: {
    contentBase: path.resolve(__dirname,'dist'),  //设置基本目录结构
    host: 'localhost',   //服务器的IP地址
    compress: true,   //服务端压缩是否开启
    port: 8080   //端口号
}
```
2. 用npm运行dev server
修改`package.json`
```json
"scripts": {
    "server": "webpack-dev-server"
}
```
终端输入`npm run server`就能运行了。webpack 3.6版本热更新不需要配置，修改文件自动会刷新页面。

### 4. module
1. 如果在js中import了一个css文件，则需要配置相应的loader。
安装两个loader：`npm install --save-dev style-loader css-loader`。
* `css-loader`解析引入的css文件
* `style-loader`将css插入到页面中
修改webpack.config.js：
```json
module: {
    rules: [
        {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
        }
    ]
}
```
* test：识别需要被转换的文件
* use：使用对应loader转换文件

### 5. plugins
1. 压缩js
压缩js使用`uglifyjs-webpack-plugin`这个插件。
先要引入插件`const UglifyJSPlugin = require('uglifyjs-webpack-plugin')`，webpack已经默认集成。
然后配置plugins：
```json
plugins: [
    new UglifyJSPlugin()
]
```
因为ugllify是生产环境使用的，`npm run server`会报错，命令行用webpack打包。

2. 自动生成index.html
安装插件`npm install --save-dev html-webpack-plugin`。
