---
title: vue2.0 踩坑
thumbnail: https://cn.vuejs.org/images/logo.png
tags:
  - vue
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

## 权限
* [vue-auth-solution](https://github.com/OneWayTech/vue-auth-solution)
* [基于vue-router的动态权限实现方案](https://segmentfault.com/a/1190000009396901)
* [Vue2.0用户权限控制解决方案](https://refined-x.com/2017/11/28/Vue2.0%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)
* [手摸手，带你用vue撸后台 系列二(登录权限篇)](https://juejin.im/post/591aa14f570c35006961acac?utm_source=gold_browser_extension)

# vue cli 3配置
## 全局less
mxin、variable这些样式是全局都需要的，每次引入比较麻烦。可以配置全局引入。
1. 插件方式安装`style-resources-loader`。`vue add style-resources-loader`。选择less。
2. 修改`vue.config.js`
```javascript
const path = require('path')

const resolve = dir => {
  return path.join(__dirname, dir)
}

module.exports = {
    // ...
    pluginOptions: {
        'style-resources-loader': {
          preProcessor: 'less',
          patterns: [
            resolve('path/to/global.less')
          ]
        }
    }
}
```

## 父子孙组件数据传递（依赖注入）动态响应
provide中的值必须是对象，inject中才能动态响应
```javascript
// 父组件
{
  provide () {
    return {
      userData: this.userData
    }
  },
  data () {
    return {
      userData: {
        userName: '',
        userCode: ''
      }
    }
  },
  methods: {
    updateUserData (record) {
      const { name, code } = record
      this.userData.userName = name
      this.userData.userCode = code
    }
  }
}

// 子孙组件
{
  inject: ['userData']
}
```
