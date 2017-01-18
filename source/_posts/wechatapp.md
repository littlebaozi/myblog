---
title: 微信小程序
date: 2017-01-16 11:30:48
thumbnail: https://mp.weixin.qq.com/debug/wxadoc/introduction/image/n.png?t=201718
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

<!--more-->

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
　　获取data属性，使用`dataset`
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
　　一个step表示一组动画，scale和rotate两个动作会同时进行
```
animation.scale(2,2).rotate(45).step()
```
　　然后通过`export`导出animation实例的动画数据，`setData`将动画数据传递给组件
```
this.setData({
  animationData:animation.export()
})
```

### 下拉刷新、底部加载

　　下拉刷新只能使用页面的滚动，`scroll-view`不能用。

1. 下拉刷新
* 下拉刷新需要在页面配置json中开启`"enablePullDownRefresh": true`。

* 在页面js中，使用`onPullDownRefresh`事件，重新请求数据，请求完数据后通过`wx.stopPullDownRefresh()`停止当前页面的下拉刷新。小程序已经移除promise，需要用到第三方promise库，推荐[es6-promise](https://github.com/stefanpenner/es6-promise)，网上有反映bluebird在android真机上报错。
```
var Promise = require('../../utils/es6-promise.min.js');

Page({
    //...
     onPullDownRefresh:function(){
        getNewItems().then(function(items){
       
            this.setData({
                'items':items
            });
            wx.stopPullDownRefresh()
        });
    }
    //...
})

function getNewItems(){
    return new Promise((reslove, reject) => {
        try{
            reslove([
                {id:1,url:'',title:'燃气报警1',content:'燃气报警器发生报警',time:'10:48'},
                {id:2,url:'',title:'燃气报警1',content:'燃气报警器发生报警',time:'09:30'},
                {id:3,url:'',title:'工单处理完成1',content:'您的报修工单已经受理',time:'10月26日'}
            ]);
        }catch(err){
            reject(err)
            console.log(err)
        }
    });
}
```

2. 滚动到底部加载
　　虽然可以使用onReachBottom，但是官方有bug，（╯‵□′）╯┴─┴ 
* bug: iOS/Android 6.3.30, 首次进入页面，如果页面不满一屏时会触发 onReachBottom ，应为只有用户主动上拉才触发；
* bug: iOS/Android 6.3.30, 手指上拉，会触发多次 onReachBottom ，应为一次上拉，只触发一次；

    所以用`scroll-view`的bindscrolltoupper事件
    
## 请求
　　`wx.request`的success返回值不是服务器的直接返回数据，实际是在res.data中。所以如果statsuCode在服务器返回的数据中，需要自己做判断：
```javascript
//配合promise
new Promise((resolve, reject) => {
    wx.request({
      url: url,
      data: data,
      method: 'GET', // OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT
      header: {'Content-Type': 'json'}, // 设置请求的 header
      success: function(res){
         let resData = JSON.parse(res.data.Data);
      
          if(resData.StatusCode == 0){
              
          }else{
              
          }
          
      },
      fail: function(res){
          
      }
    });
})
```

## 跳转
* 比如从登陆页跳转到首页（tabBar有配置首页），必须使用`wx.switchTab(OBJECT)`跳转。
* app.js 使用`wx.switchTab(OBJECT)`跳转，报错`Cannot read property 'webviewId' of undefined`。

## 问题
* WAService.js:3 navigateTo:fail url not in app.json
　　url使用的是相对路径，不是app.json里配置的复制过来就行了