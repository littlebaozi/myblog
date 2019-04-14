---
title: webpack3 入门
date: 2017-10-17 16:28:37
tags:
- webpack
categories: 
- 开发
---

## 1. 准备工作
1. 项目初始化
`npm init`初始项目

2. 新建项目目录与文件
- dist 打包后文件夹
- src 开发文件夹
 - assets
  - images
  - styles
   - index.css
 - app.js
 - index.html
- .gitignore
- webpack.config.js
- package.json

<!--more-->


## 2. webpack安装与基础配置
### 2.1 项目安装webpack
* 建议是本地安装，可以在正确的webpack版本下运行。
`npm install --save-dev webpack`

### 2.2 以下是基本的结构
```javascript
module.exports={
    entry:{},
    output:{},
    module:{},
    plugins:[], //这里是数组
    devServer:{}
}
```
* entry：入口文件配置。webpack打包的起点，可以配置多个入口。
* output：出口文件配置。是webpack打包的输出目录，只能配置一个。
* module：模块。里可以写各种loader配置，webpack 把每个文件(.css, .html, .scss, .jpg, etc.) 都作为模块处理，解析资源文件。
* plugins：插件。可以使用各种插件，解决loader无法实现的操作，比如自动生成，js压缩，热更新等。
* devServer：本地开发运行服务器配置

### 2.3 入口/出口配置
* 先引入path，`const path = require('path');`
```javascript
entry: {
    app: './src/app.js' // app是可以自己命名的，这里使用的是相对路径，相对于webpack
}
```
* output只能配置一个出口文件
```javascript
output: {
    path: path.resolve(__dirname,'dist'),  // 打包后的文件输出目录，也可以用相对路径，建议用绝对路径
    filename: 'bundle.js'  // 文件名
}
```
此时，运行`npm run build`，就能在dist文件夹下生成`bundle.js`
### 2.4 index.html
手动在dist建立`index.html`，手动引入`bundle.js`不够自动化。
可以使用`html-webpack-plugin`这个插件自动在dist目录生成`index.html`并且引入生成的`bundle.js`。

`npm install --save-dev html-webpack-plugin`安装，在`webpack.config.js`中引入。
```javascript
const path = require('path');
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin({
            hash: true, // 对js、css等文件启用hash，解决线上修改后文件缓存问题
            template: './src/index.html' // 模板文件，就是src中的index.html
        }),
    ],
    // ...
}
```
运行`npm run build`，在dist文件夹下生成了`index.html`，并且自动引入了js并带有hash值

### 2.5 本地服务器
1. 先安装，`npm install --save-dev webpack-dev-server`，然后配置`webpack.config.js`。
```javascript
devServer: {
    contentBase: path.resolve(__dirname,'dist'),  // 设置服务器的运行目录为dist
    host: 'localhost',   // 服务器的IP地址，本地可以用localhost
    compress: true,   // 服务端压缩是否开启
    port: 8080   // 运行端口号
}
```
2. 用npm运行dev server
修改`package.json`，`--open`是运行后自动打开默认浏览器。
```json
"scripts": {
    "dev": "webpack-dev-server --open"
}
```
终端输入`npm run dev`就能运行了。webpack 3.6版本热更新不需要配置，修改文件自动会刷新页面。

### 2.6 清理dist目录

## 2.6 处理css
1. 如果在js中import了一个css文件，则需要配置相应的loader，才能正确解析。
比如使用了normalize.css：`npm install --save normalize.css`。
```javascript
import "normalize.css";
import "./assets/styles/index.css";
```

需要安装两个loader：`npm install --save-dev style-loader css-loader`。
* `css-loader`解析引入的css文件
* `style-loader`将css插入到页面中
修改`webpack.config.js`：
```javascript
// ...
module: {
    rules: [
        {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
        }
    ]
}
// ...
```
* test：识别需要被转换的文件
* use：使用对应loader转换文件

loader有几种写法，你知道吗：
* 只用use
```javascript
 module: {
    rules: [
        {
            test: /\.css$/,
            use: ['style-loader','css-loader']
        }
    ]
},
```
* 只用loader
```javascript
 module: {
    rules: [
        {
            test: /\.css$/,
            loader: ['style-loader','css-loader']
        }
    ]
},
```
* use + loader
```javascript
module: {
    rules :[
        {
            test: /\.css$/,
            use: [
                {
                    loader: "style-loader"
                }, 
                {
                    loader: "css-loader"
                }
            ]
        }
    ]
},
```

### 2.7 sass/less
项目中，通常使用sass或者less提高开发效率。
1. sass
先安装相关依赖，sass编译依赖node-sass，所以也得装上。
`npm install --save-dev node-sass sass-loader`。
然后配置loader：
```
module:{
    rules :[
        {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
        },
        {
            test: /\.scss$/,
            use: ['style-loader', 'css-loader', 'sass-loader']
        }
    ]
},
```

### 2.8 自动补全CSS3前缀
这个功能用到了PostCSS。安装`npm install --save-dev postcss-loader autoprefixer`。


### 2.9 分离css
如果不想一股脑儿的把css塞进html中，可以使用`extract-text-webpack-plugin`分离css文件

### 2.10 清理未使用的CSS

## 3. JS处理

### 3.1 babel编译ES6

### 3.2 引入第三方库

### 3.3 压缩js
压缩js使用`uglifyjs-webpack-plugin`这个插件。
先要引入插件`const UglifyJSPlugin = require('uglifyjs-webpack-plugin')`，webpack已经默认集成。
然后配置plugins：
```json
plugins: [
    new UglifyJSPlugin()
]
```
因为ugllify是生产环境使用的，`npm run server`会报错，命令行用webpack打包。

## 4. 开发环境和生产环境
