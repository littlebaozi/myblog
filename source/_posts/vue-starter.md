---
title: vue2.0 踩坑
thumbnail: https://cn.vuejs.org/images/logo.png
tags:
  - vue.js
  - mvvm
categories:
  - 开发
date: 2017-02-08 09:11:22
toc: true
---


# 一、 vue-router
## 1. 登录验证
　　定义路由时增加meta字段，[查看文档](https://router.vuejs.org/zh-cn/advanced/meta.html)
```
let routes = [
    {
        path:'/home',
        name:'Home',
        component: Home,
        meta: { requiresAuth: true }
  }
]

// 登录中间验证，没有登录的情况跳转到登录页
router.beforeEach((to, from, next) =>{

  if (to.matched.some(record => record.meta.requiresAuth)) {

      if(store.state.user.userID){
         next();
      }else{
         next({
            path: '/login',
            query: { redirect: to.fullPath }
         });
      }
  } else {
      next();
  }
})
```

<!-- more -->

## 2. 默认跳转到某页
　　设置redirect字段
```
let routes = [
    {
        path:'/',
        redirect: '/home'
    }
]
```

## 3. 动态更改页面title
定义路由时，在每个路由设置中增加`meta`字段：
```javascript
new Router({
    routes: [
        {
            //...
            meta: {title: '登录'}
        }
    ]
})
```
在`main.js`中，增加：
```javascript
router.beforeEach((to, from, next) => {
  document.title = to.meta.title? to.meta.title: '首页'
  next()
})
```

## 3. 组件懒加载
1. 安装插件
```bash
npm install --save-dev babel-plugin-syntax-dynamic-import
```

2. 修改`.babelrc`，plugins中增加`syntax-dynamic-import`的配置
```javascript
"plugins": [
    "transform-runtime",
    "syntax-dynamic-import",
    ["import",{
        "libraryName": "vant",
        "style": true
      }
    ]
  ],
```

3. 引入组件
```javascript
const Login = () => import('@/pages/Login')
```

# 二、flexible自适应
1. 下载[flexible.js](https://github.com/amfe/lib-flexible/tree/master)
2. `main.js`中添加
```javascript
import '@/assets/js/flexible.js'
```
3. 使用postcss-px2rem转换px
```
npm install --save-dev postcss-loader postcss-px2rem
```
打开`build/vue-loader.conf.js`
```javascript
var px2rem = require('postcss-px2rem')

module.exports = {
	...
	postcss: [px2rem({remUnit: 75})]
}
```