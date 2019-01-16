---
title: 微信H5遇到的坑
date: 2019-01-02 10:39:26
tags: H5
categories: 开发
---

## 微信视频播放全屏
通常H5活动的视频播放需有一些交互操作。微信中视频播放会调用播放器，视频会浮在内容上面。参考文章[视频H5のVideo标签在微信里的坑和技巧](https://www.cnblogs.com/zzsdream/p/6372528.html)、[H5同层播放器接入规范](https://x5.tencent.com/tbs/guide/video.html)。

<!-- more -->

开发的时候也是用ELF的视频播放模板，但是在Android中不能播放。ELF模板是通过js判断TBS版本手动设置属性，后来直接加在video标签上就能播放了。

* iOS 10 Safari 中，video 新增了 playsinline 属性，可以使视频内联播放。IOS 10之前可以使用[iphone-inline-video](https://github.com/bfred-it/iphone-inline-video)这个库。
* Android的微信可以设置`x5-video-player-type="h5" x5-video-player-fullscreen="true"`这两个属性，调用同层播放器。
* `autoplay`在移动端无效，不能自动播放。可以用js调用`play`方法播放。
* 不播放的时候，不会显示第一帧。需要截图第一帧，先显示图片，点击开始播放后隐藏图片。

### 1. html代码
```html
<video class="video" id="video" src="http://wqs.jd.com/promote/superfestival/superfestival.mp4" preload="auto" x5-video-player-type="h5" x5-video-player-fullscreen="true" webkit-playsinline x-webkit-airplay="true" airplay="allow" playsinline poster></video>
```

### 2. js代码

```javascript


/**
 * 判断微信里 TBS 的版本
 */
var ua = window.navigator.userAgent
var TBS = ua.match(/TBS\/([\d.]+)/)
var TBS_V0 = '036849' // TBS >=036849 支持 x5-video-player-type
var TBS_V1 = '036900' // TBS >=036900 正确支持 x5videoenterfullscreen，036849 <= TBS < 036900 支持的 x5videoxxxx 事件是反的

var tbs = {}
if (TBS) {
  tbs.isTBS = true
  tbs.isRightEvent = TBS[1] >= TBS_V1
}

// 处理 tbs/QQBrowser 的兼容性
if (tbs.isTBS) {
  video.addEventListener("x5videoenterfullscreen", function () {
    if (tbs.isRightEvent) {
        // 播放器进入全屏状态
    } else {
 
    }
  })

  video.addEventListener("x5videoexitfullscreen", function () {
    
    if (tbs.isRightEvent) {
        // 播放器退出了全屏状态
    } else {
 
    }
  })
}

```

## 音频播放

* 循环播放加`loop`属性
* IOS 10以上，微信autoplay不能自动播放。需要再`wx.ready()`中调用`play`方式。

```javascript
wx.ready(function () {
    music.play();
});
```