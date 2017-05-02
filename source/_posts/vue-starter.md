---
title: vue2.0 踩坑
thumbnail: https://cn.vuejs.org/images/logo.png
tags:
  - vue.js
  - mvvm
categories:
  - 前端框架
date: 2017-02-08 09:11:22
---



# vue-router
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

## 2. 默认跳转到首页
　　设置redirect字段
```
let routes = [
    {
        path:'/',
        redirect: '/home'
    }
]
```

# 路由方式切换的tab组件