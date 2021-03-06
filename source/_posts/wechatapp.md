---
title: 微信小程序开发记录
date: 2017-01-16 11:30:48
thumbnail: https://res.wx.qq.com/wxdoc/dist/assets/img/demo.ef5c5bef.jpg
tags:
- 小程序
categories: 开发
---

入门直接看[文档](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html?t=2017112)。

## app.json
　　小程序的公共配置文件，可以配置路由、tabbar等。
* 不想手动创建目录文件，可以在`pages`中配置好路由，会自动生成目录页面
* tabBar 是一个数组，只能配置最少2个、最多5个 tab

<!--more-->

## 事件绑定
　　绑定的事件无法传参，用`data-`属性，然后用`e.currentTarget.dataset`获取

## 页面跳转
* 在wxml中设置`data-url`属性、`bindtap`事件
  ```xml
  <block wx:for="{{menuList}}" wx:key="{{index}}">
      <view class="menu-item" data-url="{{item.url}}" bindtap="navigatePage">
          <!-- ... -->
      </view> 
  </block>
  ```
  　　获取data属性，使用`dataset`。
  　　*注意*：如果`view`内部还有元素，`e.target.dataset`是无法获取`data`属性的，需要使用`currentTarget`。
  ```
  Page({
      navigatePage: function(e){
          var url = e.currentTarget.dataset.url; //dataset获取data属性
          wx.navigateTo({url: url})
    }
  })
  ```

* 比如从登陆页跳转到首页（tabBar有配置首页），必须使用`wx.switchTab(OBJECT)`跳转。
* 已经打开的tab，调用`switchTab`不会触发`onShow`，只能手动调用`onShow`。
* `wx.reLaunch`在1.1.0基础库中，安卓能够返回上一级页面，会报错`navigateBack with an unexist webviewId`
  ```javascript
  wx.switchTab({  
      url: '../index/index',  
      success: function (e) {  
      var page = getCurrentPages().pop();  
      if (page == undefined || page == null) return;  
      page.onShow();  
  }  
  ```
* app.js 使用`wx.switchTab(OBJECT)`跳转，报错`Cannot read property 'webviewId' of undefined`。

* 页面无法跳转的问题

  假设有A、B两个页面，A navigate B，B redirect A。当页面栈是5个的时候，A就无法导航到B了。要解决这个问题，B navigateBack A。
{% asset_img routestack_5.png route %}
  > 代码片段：https://developers.weixin.qq.com/s/oNu0X2mg7Ud7
  A navigate B，B navigate A 也是同样的问题。

  A navigate B，B navigate C，C redirect D，D redirect B，在非代码片段的项目中，会在10个页面栈之后，无法跳转。
  {% asset_img routestack_10.png route %}
  > 代码片段：https://developers.weixin.qq.com/s/kTue22mh7rdW


## js动画
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

## 下拉刷新、底部加载

　　下拉刷新只能使用页面的滚动，`scroll-view`不能用。

1. 下拉刷新
* 下拉刷新需要在页面配置json中开启`"enablePullDownRefresh": true`。

* 在页面js中，使用`onPullDownRefresh`事件，重新请求数据，请求完数据后通过`wx.stopPullDownRefresh()`停止当前页面的下拉刷新。

2. 滚动到底部加载
　　虽然可以使用onReachBottom，但是官方有bug，（╯‵□′）╯┴─┴ 
* bug: iOS/Android 6.3.30, 首次进入页面，如果页面不满一屏时会触发 onReachBottom ，应为只有用户主动上拉才触发；
* bug: iOS/Android 6.3.30, 手指上拉，会触发多次 onReachBottom ，应为一次上拉，只触发一次；
    
```
Page({
    //...
     onPullDownRefresh:function(){
        // request
        wx.stopPullDownRefresh()
    },
    onReachBottom: function(){
        // ...
    }
    //...
})
```

## 请求
　　`wx.request`的success返回值不是服务器的直接返回数据，实际是在res.data中。所以如果statsuCode在服务器返回的数据中，需要自己做判断。下面通过[es6-promise](https://github.com/stefanpenner/es6-promise)封装了一下请求
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

## picker
　　index表示数据的下标，用来处理选中，需要在data中保存记录。当数据是object value时，需要设置`range-key`，否则弹出框显示[object objec]。

```html
<!-- 
value：弹出框中默认选中的值的index；
range-key： 弹出框中需要显示的key值
-->
<picker bindchange="bindPickerChange" value="{{index}}" range="{{objectArray}}" range-key="name">
    <view >
        <text>请选择：</text>
        <view >
            <text>{{objectArray[index].name}} </text>
        </view>
    </view>
</picker>
```

```js
Page({
    data: {
        index:0, //默认选中第几个
        objectArray:[
          {
            id: 0,
            name: '美国'
          },
          {
            id: 1,
            name: '中国'
          },
          {
            id: 2,
            name: '巴西'
          },
          {
            id: 3,
            name: '日本'
          }
        ]
    },
    bindPickerChange: function(e){
        //选择Change事件，改变选中index
        this.setData({
            index: e.detail.value
        });
    }
})
```

## input
　　input的type类型不同弹出键盘的类型不同
* text：全键盘
* mumber：纯数字键盘
* digit：待小数点的数字键盘
* 重置高度使用em单位

## scroll-view
### 1. 上下布局，下面scroll-view高度自动撑满：
* 一种方式是计算高度，设置scroll-view高度。但是设置了tabbar的话，没有显示tabbar的页面，windowHeight是减去tabbar的高度。可以新增的screenHeight。然而如果用rpx布局，计算误差还是存在的。
* 另一种方式是css来布局。

```html
<view class="location-section">当前位置</view>
<scroll-view class="scroll-section" scroll-y="true" ></scroll-view>
```
```css
.location-section{
    height: 60rpx;
}
.scroll-section{
    height: 100%;
    padding-top: 60rpx;
    position: absolute;
    top:0;
    box-sizing: border-box;
}
```

### 2. 横向scroll
* `scroll-view`一定要设置宽度和`white-space: nowrap`
* 子元素一定要设置宽度和`display:inline-block`

## 兼容性
　　screenHeight, SDKVersion 基础库版本 1.1.0 开始支持, 微信客户端 6.5.6 版本开始支持。
所以在需要设置scrollview高度与屏幕高度相关时，旧版本只能windowHeight，但在设置tabbar时，会减去tabbar的高度。
tabbar高度是`56px`，需要手动加上。

## 问题
* WAService.js:3 navigateTo:fail url not in app.json
　　url使用的是相对路径，不是app.json里配置的复制过来就行了。
* <del>小程序已经移除promise，需要用到第三方promise库</del>，推荐[es6-promise](https://github.com/stefanpenner/es6-promise)，网上有反映bluebird在android真机上报错。</del>更新之后Promise可以直接使用了。
* Android能访问接口，IOS不能访问接口并且报错“此服务器的证书无效”。那应该是https的证书问题。网上说，startcom的证书在IOS10上无法使用。

## 小程序框架
### vant-weapp
#### [dialog](ps://youzan.github.io/vant-weapp/#/dialog)
组件调用dialog，并启用`async-close`时，关闭确定按钮loading：
``` javascript
this.selectComponent('#dialog').stopLoading(); // #dialog是Dialog组件上设置的id
```