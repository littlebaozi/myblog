---
title: 网页直播rtsp视频 node.js+ffmpeg+websocket+flv.js
tags:
  - 直播
  - Node.js
  - H5
category: 开发
date: 2019-10-23 15:39:16
---


项目中需要将监控摄像头的画面播放在网页中，这就需要把rtsp协议的视频转换成前端网页能够播放的格式。

## 查阅资料：
* [HTML5视频监控技术预研](https://blog.gmem.cc/research-on-html5-video-surveillance)
* [如何不花钱让html5播放rtsp视频流（第一弹）](https://juejin.im/post/5d71c57be51d45620d2cb955)、[如何不花钱让html5播放rtsp视频流（第二弹）](https://juejin.im/post/5d8487926fb9a06b04723934)
* [浏览器播放rtsp视频流解决方案](https://juejin.im/post/5d183a71f265da1b6e65b8ff)
* [chrome中使用vxg播放rtsp视频流](https://juejin.im/post/5d8dd975f265da5b5b6c5563)
* [HTML5 播放 RTSP 视频](https://hpdell.github.io/%E7%BC%96%E7%A8%8B/html5-rtsp/index.html)
* [几种浏览器播放RTSP视频流的方案](https://bnlt.org/2019/%E5%87%A0%E7%A7%8D%E6%B5%8F%E8%A7%88%E5%99%A8%E6%92%AD%E6%94%BERTSP%E8%A7%86%E9%A2%91%E6%B5%81%E7%9A%84%E6%96%B9%E6%A1%88/)
* [网络摄像机直播](https://caraws.github.io/2018/05/06/%E7%BD%91%E7%BB%9C%E6%91%84%E5%83%8F%E6%9C%BA%E7%9B%B4%E6%92%AD/)

<!-- more -->

## 小科普
rtsp、rtmp、hls都是流媒体传输协议。

>HLS （ HTTP Live Streaming）苹果公司提出的流媒体协议，直接把流媒体切片成一段段，信息保存到m3u列表文件中，可以将不同速率的版本切成相应的片；播放器可以直接使用http协议请求流数据，可以在不同速率的版本间自由切换，实现无缝播放；省去使用其他协议的烦恼。缺点是延迟大小受切片大小影响，不适合直播，适合视频点播。

>RTSP （Real-Time Stream Protocol）由Real Networks 和 Netscape共同提出的，基于文本的多媒体播放控制协议。RTSP定义流格式，流数据经由RTP传输；RTSP实时效果非常好，适合视频聊天，视频监控等方向。

> RTMP（Real Time Message Protocol） 有 Adobe 公司提出，用来解决多媒体数据传输流的多路复用（Multiplexing）和分包（packetizing）的问题，优势在于低延迟，稳定性高，支持所有摄像头格式，浏览器加载 flash插件就可以直接播放。

> 总结：HLS 延迟大，适合视频点播；RTSP虽然实时性最好，但是实现复杂，适合视频聊天和视频监控；RTMP强在浏览器支持好，加载flash插件后就能直接播放，所以非常火，相反在浏览器里播放rtsp就很困难了。


## 放弃的方案
### rtsp转rtmp播放
播放rtmp视频需要flash，较新版本的Chrome已经默认不支持flash了。

### vlc插件
使用vlc插件可以直接播放rtsp协议视频。然而，一方面要先安装vlc播放器，另一方面较新版本Chrome和Firefox都不支持NPAPI了。

## 备选方案
**如何不花钱让html5播放rtsp视频流（第二弹）**这篇中同样是先转码rtsp，只不过是使用`node-rtsp-stream-jsmpeg`将视频推流成图片不停绘制canvas。

## 选中方案
最后采取了**HTML5播放RTSP视频**这篇文章的方案。

* 前端将摄像头视频地址发送到服务端
* 服务端使用ffmpeg转码为flv
* 通过websocket实时推流到前端网页上
* 前端使用[flv.js](https://github.com/bilibili/flv.js)播放视频

## 存在问题
浏览器切换标签或者最小化，视频会暂停播放导致延迟。目前解决办法是监听`visibilitychange`，重新显示时手动调整时间。

### 服务端代码
```javascript
const express = require('express');
const expressWebSocket = require("express-ws");
import ffmpeg from "fluent-ffmpeg";
import webSocketStream from "websocket-stream/stream";
import WebSocket from "websocket-stream";
import * as http from "http";

function localServer() {
  let app = express();
  app.use(express.static(__dirname));
  expressWebSocket(app, null, {
    perMessageDeflate: true
  });
  app.ws("/rtsp/:id/", rtspRequestHandle)
  app.listen(8888);
  console.log("express listened")
}

function rtspRequestHandle(ws, req) {
  console.log("rtsp request handle");
  const stream = webSocketStream(ws, {
    binary: true,
    browserBufferTimeout: 1000000
  }, {
    browserBufferTimeout: 1000000
  });
  let url = req.query.url;
  console.log("rtsp url:", url);
  console.log("rtsp params:", req.params);
  
  let ffmpegCommand = ffmpeg(url)
    .addInputOption("-rtsp_transport", "tcp", "-buffer_size", "102400")  // 这里可以添加一些 RTSP 优化的参数
    .on("start", function () {
      console.log(url, "Stream started.");
      ws.send('')
    })
    .on("codecData", function () {
      console.log(url, "Stream codecData.")
      // 摄像机在线处理
    })
    .on("error", function (err) {
      console.log(url, "An error occured: ", err.message);
      stream.end();
    })
    .on("end", function () {
      console.log(url, "Stream end!");
      stream.end();
      // 摄像机断线的处理
    })
    .outputFormat("flv").videoCodec("copy").noAudio();

  stream.on("close", function () {
    ffmpegCommand.kill('SIGKILL');
  });
  try {
    ffmpegCommand.pipe(stream);
  } catch (error) {
    console.log(error);
  }
 
}

localServer()
```

### 前端代码
```html
<template>
  <el-button @click="playVideo">播放</el-button>
  <el-button @click="destroyVideo">断开</el-button>
  <video ref="player" width="100%" height="500"></video>
</template>

<script>
import io from 'socket.io-client'
import flvjs from 'flv.js'

export default {
  data () {
    return {
      player: null,
      streamId: '1',
      rtspUrl: 'rtsp://wowzaec2demo.streamlock.net/vod/mp4:BigBuckBunny_115k.mov'
    }
  },
  methods: {
    playVideo () {
      if (flvjs.isSupported()) {
        let video = this.$refs.player
        if (video) {
          if (this.player) {
            this.destroyVideo()
          }
          this.player = flvjs.createPlayer({
            type: 'flv',
            isLive: true,
            enableStashBuffer: false,
            url: `ws://localhost:8888/rtsp/${this.streamId}/?url=${this.rtspUrl}`
          })
          this.player.attachMediaElement(video)
          try {
            this.player.load()
            this.player.play()
            console.log('play')
          } catch (error) {
            console.log(error)
          }

          if (document.hidden !== undefined) {
            document.addEventListener('visibilitychange', this.justifyPlayTime)
          }
        }
      }
    },
    justifyPlayTime () {
      console.log(document.hidden)
      if (!document.hidden) {
        // 显示
        let video = this.$refs.player
        let buffered = video.buffered
        if (buffered.length > 0) {
          let end = buffered.end(0)
          if (end - video.currentTime > 0.15) {
            video.currentTime = end - 0.1
          }
        }
      } else {
        // 隐藏
      }
    },
    destroyVideo () {
      if(this.player) {
        this.player.pause()
        this.player.unload()
        this.player.detachMediaElement()
        this.player.destroy()
        this.player = null
        document.removeEventListener('visibilitychange', this.justifyPlayTime)
      }
    }
  }
}
</script>
```