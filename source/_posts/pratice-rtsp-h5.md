---
title: 网页直播rtsp视频 node.js+ffmpeg+websocket+flv.js
tags:
  - 直播
  - Node.js
  - H5
category: 开发
date: 2019-10-23 15:39:16
---


项目中需要将监控摄像头的画面在网页中播放，摄像头的流媒体协议是RTSP的，现代浏览器都无法直接播放，这就需要增加一套转流的服务。

<!-- more -->

## 查阅资料：
* [HTML5视频监控技术预研](https://blog.gmem.cc/research-on-html5-video-surveillance)。这篇文章最详尽，编解码、服务端、客户端技术都有写。
* [HTML5 播放 RTSP 视频](https://hpdell.github.io/%E7%BC%96%E7%A8%8B/html5-rtsp/index.html)。主要采用了这篇中的方案。
* [如何不花钱让html5播放rtsp视频流（第一弹）](https://juejin.im/post/5d71c57be51d45620d2cb955)、[如何不花钱让html5播放rtsp视频流（第二弹）](https://juejin.im/post/5d8487926fb9a06b04723934)
* [浏览器播放rtsp视频流解决方案](https://juejin.im/post/5d183a71f265da1b6e65b8ff)
* [几种浏览器播放RTSP视频流的方案](https://bnlt.org/2019/%E5%87%A0%E7%A7%8D%E6%B5%8F%E8%A7%88%E5%99%A8%E6%92%AD%E6%94%BERTSP%E8%A7%86%E9%A2%91%E6%B5%81%E7%9A%84%E6%96%B9%E6%A1%88/)
* [网络摄像机直播](https://caraws.github.io/2018/05/06/%E7%BD%91%E7%BB%9C%E6%91%84%E5%83%8F%E6%9C%BA%E7%9B%B4%E6%92%AD/)

## 流媒体协议
主流的用于承载视频流的流媒体协议包括：
{% raw %}
<table>
  <tr><th>协议</th><th>说明</th></tr>
  <tr>
    <td>HLS</td>
    <td>
      HTTP实时流（HTTP Live Streaming），由苹果开发，基于HTTP协议。

      HLS的工作原理是，把整个流划分成一个个较小的文件，客户端在建立流媒体会话后，基于HTTP协议下载流片段并播放。客户端可以从多个服务器（源）下载流。

      在建立会话时，客户端需要下载extended M3U (m3u8) 播放列表文件，其中包含了MPEG-2 TS（Transport Stream）容器格式的视频的列表。在播放完列表中的文件后，需要再次下载m3u8，如此循环。

      此协议在移动平台上支持较好，目前的Android、iOS版本都支持。

      此协议的重要缺点是高延迟（5s以上通常），要做到低延迟会导致频繁的缓冲（下载新片段）并对服务器造成压力，不适合视频监控。

      播放HLS流的HTML代码片段：
      ```html
      <video src="http://movie.m3u8" height="329" width="480"></video>
      ```
    </td>
  </tr>
  <tr>
    <td>RTMP</td>
    <td>
      实时消息协议（Real Time Messaging Protocol），由Macromedia（Adobe）开发。此协议实时性很好，需要Flash插件才能在客户端使用，但是Adobe已经打算在不久的将来放弃对Flash的支持了。最新版Chrome已经禁用flash了。

      有一个开源项目[HTML5 FLV Player](https://github.com/Bilibili/flv.js)，它支持在没有Flash插件的情况下，播放Flash的视频格式FLV。此项目依赖于MSE，支持以下特性：
      * 支持H.264 + AAC/MP3编码的FLV容器格式的播放
      * 分段（segmented）视频播放
      * 基于HTTP的FLV低延迟实时流播放
      * 兼容主流浏览器
      * 资源占用低，可以使用客户端的硬件加速
    </td>
  </tr>
  <tr>
    <td>RTSP</td>
    <td>
      实时流协议（Real Time Streaming Protocol），由RealNetworks等公司开发。此协议负责控制通信端点（Endpoint）之间的媒体会话（media sessions） —— 例如播放、暂停、录制。通常需要结合：实时传输协议（Real-time Transport Protocol）、实时控制协议（Real-time Control Protocol）来实现视频流本身的传递。

      大部分监控摄像头都是RTSP协议的。大部分浏览器没有对RTSP提供原生的支持。

      RTSP 2.0版本目前正在开发中，和旧版本不兼容。
    </td>
  </tr>
  <tr>
    <td>MPEG-DASH</td>
    <td>
      基于HTTP的动态自适应流（Dynamic Adaptive Streaming over HTTP），它类似于HLS，也是把流切分为很小的片段。DASH为支持为每个片段提供多种码率的版本，以满足不同客户带宽。

      协议的客户端根据自己的可用带宽，选择尽可能高（避免卡顿、重新缓冲）的码率进行播放，并根据网络状况实时调整码率。

      DASH不限制编码方式，你可以使用H.265, H.264, VP9等视频编码算法。

      Chrome 24+、Firefox 32+、Chrome for Android、IE 10+支持此格式。

      类似于HLS的高延迟问题也存在。
    </td>
  </tr>
  <tr>
    <td>WebRTC</td>
    <td>
      WebRTC是一整套API，为浏览器、移动应用提供实时通信（RealTime Communications）能力。它包含了流媒体协议的功能，但是不是以协议的方式暴露给开发者的。

      WebRTC支持Chrome 23+、Firefox 22+、Chrome for Android，提供Java / Objective-C绑定。

      WebRTC主要有三个职责：

      * 捕获客户端音视频，对应接口MediaStream（也就是getUserMedia）
      * 音视频传输，对应接口RTCPeerConnection
      * 任意数据传输，对应接口RTCDataChannel

      WebRTC内置了点对点的支持，也就是说流不一定需要经过服务器中转。
    </td>
  </tr>
</table>
{% endraw %}

[引用](https://blog.gmem.cc/research-on-html5-video-surveillance)

## 方案选择

![HTML5视频监控技术预研](https://gmem.site/wp-content/uploads/2017/08/h5vs-dataflow.png)

### 放弃的方案
1. 付费的
如果公司有钱，可以购买别人的流媒体解决方案：[easyNVR](https://www.easynvr.com/)、[青柿](https://www.liveqing.com/)、[Streamedian](https://streamedian.com/)

2. RTSP转RTMP+Flash
此方案可以参考[如何不花钱让html5播放rtsp视频流（第一弹）](https://juejin.im/post/5d71c57be51d45620d2cb955)。

  * 缺点：播放RTMP视频需要flash，较新版本的Chrome已经默认不支持flash了。

3. VLC插件
  * 优点：使用VLC插件可以直接播放rtsp协议视频。
  * 缺点：要先安装VLC播放器，较新版本Chrome和Firefox都不支持NPAPI了。


目前来看，不花钱延迟小适合现代浏览器的技术方案是：需要一个服务端将RTSP协议的视频转码成网页能够直接播放的媒体流。


### 选中方案
最后采取了[HTML5 播放 RTSP 视频](https://hpdell.github.io/%E7%BC%96%E7%A8%8B/html5-rtsp/index.html)这篇文章的方案。

* 前端将摄像头视频地址发送到服务端
* 服务端使用`FFmpet`将RTSP转码为flv片段。
* 通过websocket实时推流到前端网页上
* 前端使用[flv.js](https://github.com/bilibili/flv.js)转码播放视频。

`flv.js`使用的是[MSE](https://developer.mozilla.org/zh-CN/docs/Web/API/Media_Source_Extensions_API)技术。`flv.js`直接用js转码flv流转为ISO BMFF（MP4）片断，然后把视频片段给video元素播放。

**存在问题**：浏览器切换标签或者最小化，视频会暂停播放导致延迟。目前解决办法是监听`visibilitychange`，重新显示时手动调整时间。

### 备选方案
[如何不花钱让html5播放rtsp视频流（第二弹）](https://juejin.im/post/5d8487926fb9a06b04723934)这篇中同样是使用`FFmpeg`先转码rtsp，只不过是使用`node-rtsp-stream-jsmpeg`将视频推流成图片不停绘制canvas。
