---
title: iview-admin后台管理开发实践
date: 2020-01-07 11:10:09
tags: 
  - vue
  - iview
  - 后台管理
categories:
  - 开发
---

[iview-admin](https://github.com/iview/iview-admin)在基于Vue.js的后台管理框架中功能比较完善。

此篇基于
* iview-admin**2.5.0**
* vue cli 4.1.1

<!-- more -->

## 增加开发、编译环境
修改参照[vue cli文档](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F)

* .env(默认环境)
```
# 所有环境默认

# 页面 title 前缀
VUE_APP_TITLE=智能设备管理平台
```
* .env.development(开发环境)
```

# 开发环境

# 页面 title 前缀
VUE_APP_TITLE=智能设备管理平台 Dev

# 网络请求公用地址
VUE_APP_API=VUE_APP_API==http://develop.com
```

* .env.mock(mock开发环境)
```
# 开发环境Mock

# 页面 title 前缀
VUE_APP_TITLE=智能设备管理平台 Dev Mock

# 网络请求公用地址
VUE_APP_API=VUE_APP_API==http://develop.com

# 标记当前是 Mock 
VUE_APP_API_MODE=mock
```

* .env.staging(预发布环境)
```
# 预发布

# 页面 title 前缀
VUE_APP_TITLE=智能设备管理平台 Stage

# 指定构建模式
NODE_ENV=production

# 网络请求公用地址
VUE_APP_API==http://develop.com
```

* .env.production(发布环境)
```
# 正式发布

# 指定构建模式
NODE_ENV=production

# 网络请求公用地址
VUE_APP_API=http://product.com
```

### 增加`VUE_APP_TITLE`
有时候发昏打包错了都不知道。所以就增加了`VUE_APP_TITLE`。title中增加当前环境。
```
# 页面 title 前缀
VUE_APP_TITLE=智能设备管理平台 Dev
```

同时，修改`src/config/index.js`：
```javascript
export default {
  /**
   * @description 配置显示在浏览器标签的title
   */
  title: process.env.VUE_APP_TITLE,
}
```

### 增加`VUE_APP_API`
用于自动根据开发环境，配置不同环境下的接口地址。

修改`src/config/index.js`：
```javascript
export default {
    /**
   * @description api请求基础路径
   */
  baseUrl: process.env.VUE_APP_API,
}
```

修改`src/libs/api.request.js`：
```javascript
const baseUrl = config.baseUrl
```

### 增加`VUE_APP_API_MODE=mock`
修改`src/main.js`：
```javascript
if (process.env.VUE_APP_API_MODE === 'mock') require('@/mock')
```

## 全局引入less文件
`mixin.less`之类的样式文件，最方便的是全局引入，不然每个样式中都要引入一遍。

1. 命令行中`vue add style-resources-loader`。选择less。
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