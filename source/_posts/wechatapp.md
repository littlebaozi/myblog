---
title: 微信小程序
date: 2017-01-16 11:30:48
tags:
- 微信小程序
categories: 
- 框架
---

入门直接看[文档](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html?t=2017112)。

## app.json
    小程序的公共配置文件，可以配置路由、tabbar等。
* 不想手动创建目录文件，可以在`pages`中配置好路由，会自动生成目录页面
* tabBar 是一个数组，只能配置最少2个、最多5个 tab

## 开发
### 页面跳转
    在wxml中设置`data-url`属性、`bindtap`事件
```xml
<block wx:for="{{menuList}}" wx:key="{{index}}">
    <view class="menu-item" data-url="{{item.url}}" bindtap="navigatePage">
        <!-- ... -->
    </view> 
</block>
```

```
Page({
    navigatePage: function(e){
        var url = e.currentTarget.dataset.url; //dataset获取data属性
        wx.navigateTo({url: url})
   }
})
```

### js动画
1. 动画数据
用animationData存储动画数据
```javasript
Page({
  data: {
    animationData: {}
  }
})
```

```xml
<view animation="{{animationData}}"></view>
```
2. 创建动画实例
```javascript
var animation = wx.createAnimation({
    duration: durationTime,
})
```