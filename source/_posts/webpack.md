---
title: webpack配置
date: 2016-02-2 10:42:08
thumbnail: https://webpack.github.io/assets/what-is-webpack.png
tags:
- webpack
categories: 
- 开发
---

## 命令操作

```json
webpack -watch
webpack --config webpack.config.js
wabpack -p
```

<!--more-->

## 举个栗子

```json
var webpack = require('webpack');

var path = require('path'),
    pagePath = path.join(__dirname, '/src/js/page/'),
    libPath = path.join(__dirname, '/src/js/lib/'),
    modulePath = path.join(__dirname, '/src/js/module/'),
    pathToReact = path.join(__dirname, '/node_modules/react/dist/react-width-addons.min.js'),
    pathToReactDom = path.join(__dirname, '/node_modules/react-dom/dist/react-dom.min.js'),
    pathToZepto =  path.join(libPath, 'zepto.min.js');
module.exports   = {
    entry: {
        'index/index':pagePath+'index/index',
        'index/indexfixing':pagePath+'index/indexfixing',
        'fixing/index':pagePath+'fixing/index',
        'fixing/brand':pagePath+'fixing/brand',
        'fixing/kind':pagePath+'fixing/kind',
        'fixing/malfunction':pagePath+'fixing/malfunction',
        vendor: ['zepto']
    },
    output: {
        path: __dirname+'/js/page/',
        filename: "[name].js"
    },
    resolve: {
        extensions: ['', '.js', '.jsx'],
        modulesDirectories: ['src/js/lib','src/js/mobule' ,'node_modules'],
        alias: {
            'zepto':pathToZepto,
            'cookie':modulePath+'cookie',
            'dialog':modulePath+'dialog',
            'tomtouch':modulePath+'tomtouch',
            'pageloading':modulePath+'pageloading',
            'flash':modulePath+'flash'
        }
    },
    module: {
        noParse: [pathToReact,pathToReactDom,pathToZepto],
        loaders: [
            {
                test: /\.js[x]?$/,
                loader: "babel",
                exclude: /node_modules/,
                query:
                {
                    presets:['react']
                }
            }
        ]
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin("vendor", "common.js")
    ]
};
```
## 配置细节
### entry
　　配置要打包的文件的入口；可以配置多个入口文件
```json
entry = {
    "/demo/button": "demo/button/index.jsx"),
    "/demo/grid": "demo/grid/index.jsx")
}
```

### resolve
　　配置文件后缀名，除了js，还有jsx、coffee等等。除了这个功能还可以配置其他有用的功能，由于我还不完全了解，有知道的朋友欢迎指教。
    
### output
　　配置输出文件的路径，文件名等。

### module(loaders)
　　配置要使用的loader。对文件进行一些相应的处理。比如babel-loader可以把es6的文件转换成es5。
大部分的对文件的处理的功能都是通过loader实现的。loader就相当于gulp里的task。loader可以用来处理在入口文件中require的和其他方式引用进来的文件。loader一般是一个独立的node模块，要单独安装。
* loader配置项
```json
/**
* 注意是正则表达式，不要加引号，匹配要处理的文件loader: 'eslint-loader',  
* 要使用的loader，"-loader"可以省略include: [path.resolve(__dirname, "src/app")], 
* 把要处理的目录包括进来exclude: [nodeModulesPath] 排除不处理的目录  
*/
test: /\.(js|jsx)$/,
```

### plugins
　　顾名思义，就是配置要使用的插件。不过plugin和loader有什么差别还有待研究。来看一个使用plugin的例子：
```json
plugins: [
    //压缩打包的文件new webpack.optimize.UglifyJsPlugin({
      compress: {
        //supresses warnings, usually from module minification
        warnings: false
      }
    }),
    //允许错误不打断程序new webpack.NoErrorsPlugin(),
    //把指定文件夹xia的文件复制到指定的目录new TransferWebpackPlugin([
      {from: 'www'}
    ], path.resolve(__dirname,"src"))
  ]
```

### 如何压缩输出的文件
```json
plugins: [
    //压缩打包的文件
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        //supresses warnings, usually from module minificationwarnings: false
      }
    })]
```

### requirejs的写法
```
define(['react','react-dom','cookie','dialog','pageloading'],function(React,ReactDOM,cookie,dialog,pagelaoding){
    Zepto(function($){
		//codes
	})
});
```