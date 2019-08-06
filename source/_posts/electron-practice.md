---
title: Electron实践
date: 2019-07-28 15:39:19
tags: electron
categories: 开发
---

##  electron-react-boilerplate
### ant design 
1. less
``` javascript
{
  test: /\.less$/,
  include: /node_modules/,
  use: [
    {
      loader: 'style-loader'
    },
    {
      loader: 'css-loader'
    },
    {
      loader: 'less-loader',
      options: {
        modifyVars: {
          //这是自定义主题色等等...
        },
        javascriptEnabled: true
      }
    }
  ]
}
```
