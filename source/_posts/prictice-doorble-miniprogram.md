---
title: 蓝牙门禁配置微信小程序项目总结
date: 2019-11-18 15:56:31
tags: 
  - 小程序
  - 蓝牙
category: 开发
toc: true
---

## 项目结构
```bash
├─ api # 接口
├─ components # 自定义组件
├─ images # 切图等图片
├─ miniprogram_npm
│   └─ vant-weapp # 用了vant-weapp
├─ pages # 页面目录
│   ├─ device # 搜索设备列表
│   ├─ menus # 菜单
│   ├─ result # 设置结果
│   ├─ resetpwd # 首次连接重置密码
│   ├─ ... # 其他页面
├─ styles # 样式
├─ utils # 工具函数 请求函数 表单验证函数等
├─ .gitignore
├─ app.js
├─ app.json
├─ app.wxss
├─ config.js # 一些配置
├─ package-lock.json
├─ package.json
├─ project.config.json
├─ READEME.md
├─ sitemap.json
```

<!-- more -->

{% asset_img page.jpeg 页面流程 %}

## 蓝牙操作
### 设备查找
* 设备断开时，可能在其他页面上，为了用户方便，可以监听断开时，自动跳转到设备列表页。

```javascript
wx.openBluetoothAdapter({
  success: () => {
    wx.onBLEConnectionStateChange((connectStateRes) => {
      // 该方法回调中可以用于处理连接意外断开等异常情况
      if (!connectStateRes.connected) {
        // 断开提示...

        app.resetPassword()

        let currentPages = getCurrentPages()
        let route = currentPages[currentPages.length - 1].route // 获取当前路由，前面是不带“/”的

        if (route != 'pages/device/device') {
          wx.reLaunch({
            url: '/pages/device/device'
          })
        }
      }
    })
  }
)
```

* 事先知道设备的service uuid且设备广播了service的uuid，那么可以直接过滤设备。
* 小程序文档提示“此操作比较耗费系统资源”，所以设置一个倒计时定时器stop搜索。
* 可能周围存在多个设备，为了方便用户找到设备，根据RSSI值排序。

```javascript
wx.startBluetoothDevicesDiscovery({
  services: [deviceServiceId], // app.globalData.serviceId
  interval: 300,
  success: (discoveryStartRes) => {
    /* 
      停止搜索倒计时
    */
    this.pageData.searchTimer = setTimeout(() => {
      wx.getBluetoothAdapterState({
        success: (adapterStateRes) => {
          // 判断是否在搜索状态
          if (adapterStateRes.discovering ) {
            wx.stopBluetoothDevicesDiscovery({
              success: (discoveryStopRes) => {
                console.warn(discoveryStopRes)
              }
            })
          }

          if (this.data.deviceList.length === 0) {
            // 没有找到设备的话，提示未找到设备
          }
        },
        complete: () => {
        }
      })
    }, this.pageData.searchTimeout*1000);

    /* 
      搜索到设备就按照信号强度从高到低排序
    */
    wx.onBluetoothDeviceFound((foundResult) => {
      console.warn(foundResult)
      let deviceData = foundResult.devices
      let devices = this.data.deviceList
      let newDevices = devices.concat(deviceData).sort((a, b) => {
        // 降序
        return b.RSSI - a.RSSI
      })
      console.log(newDevices)
      this.setData({
        deviceList: newDevices
      })
    });
  },
});
```

## 数据交互
此次要求发送的数据是JSON字符串的格式。

特别注意编码问题，硬件那边接收的是UTF-8的字符串。如果没有中文，转unicode也行；如果有中文，需要转UTF-8，否则硬件接收那边会乱码。

> 对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。

字符编码又是一门学问，参考[Unicode与JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)、[字符编码笔记：ASCII，Unicode 和 UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)。

* JSON转UTF-8字符串。由于需要分包传输，所以增加了头标识和数据长度位，能够让硬件接收程序判断数据开始和结束。
```javascript
/**
 * 将字符串转码为UTF-8编码的数组
 * @param {String} text 字符串
 */
export function encodeUtf8(text) {
  const code = encodeURIComponent(text);
  const bytes = [];
  for (var i = 0; i < code.length; i++) {
    const c = code.charAt(i);
    if (c === '%') {
      const hex = code.charAt(i + 1) + code.charAt(i + 2);
      const hexVal = parseInt(hex, 16);
      bytes.push(hexVal);
      i += 2;
    } else bytes.push(c.charCodeAt(0));
  }
  return bytes;
}

/**
 * utf-8字符数组转字符串
 * @param {Array} bytes utf-8字符数组
 */
export function decodeUtf8(bytes) {
  var encoded = "";
  for (var i = 0; i < bytes.length; i++) {
    encoded += '%' + bytes[i].toString(16);
  }
  return decodeURIComponent(encoded);
}

/**
 * JSON转UTF-8字符串
 * @param {Object} data JSON对象
 */
export const JSONtoData = (data) => {
  let dataCode = JSON.stringify(data)
  let dataLengthString = dataCode.length.toString() // 获取数据内容长度并转为字符串
  let headCode = `HEAD${'0'.repeat(8 - dataLengthString.length)}${dataLengthString}` // HEAD（头标识）+数据长度（8位）。比如数据长度为1时：HEAD00000001。
  let allCode = headCode + dataCode // 头部
  return encodeUtf8(allCode)
}
```


### 数据分包
> 小程序不会对写入数据包大小做限制，但系统与蓝牙设备会限制蓝牙4.0单次传输的数据大小，超过最大字节数后会发生写入错误，建议每次写入不超过20字节。

使用`splice(0, 20)`切割字符串，递归调用发送API，直到字符串被切割完。
```javascript
BLEWriteData(data) {
  this.BLESendData(JSONtoData(data))
},
BLESendData(code) {
  let sendPackage= code.splice(0, 20) // 20字节分包
  let buffer = arrayToBuffer(sendPackage)
  wx.writeBLECharacteristicValue({
    deviceId: deviceId,
    serviceId: serviceId,
    // 这里的 characteristicId 需要在 getBLEDeviceCharacteristics 接口中获取
    characteristicId: characteristicWriteId,
    // 这里的value是ArrayBuffer类型
    value: buffer,
    success: () => {
      if (code.length) {
        this.BLESendData(code)
      }
    },
    complete: (writeRes) => {
      // console.log('BLE write complete', writeRes)
    }
  })
}
```

### 数据接收
由于不同功能的页面是不同的method，需要处理响应数据，所以在每个页面写了onBLECharacteristicValueChange。这部分还没想到更好的处理方法，暂时先麻烦点。
```javascript
wx.onBLECharacteristicValueChange((characteristicRes) => {
  let resData = bufferToString(characteristicRes.value)
  if (/^HEAD/.test(resData)) {
    // 检测头部标识
    this.pageData.responseLength = parseInt(resData.slice(4, 12)) // 获取数据长度
    this.pageData.responseStr = resData.slice(12) // 将后面的数据暂存到responseStr中
  } else {
    // 如果不是头部，那么继续拼接数据
    this.pageData.responseStr += resData;
    // 读完数据
    if (this.pageData.responseLength == this.pageData.responseStr.length) {
      let jsonData = JSON.parse(this.pageData.responseStr)
      this.resetData() // 数据重置
      this.resetLoading()
      // 根据method方法判断是否是当前页面的操作
      switch (jsonData.method) {
        case 'netcfg': // 表示网络配置操作
          if (jsonData.statuscode == 0) {
            // 成功redirect，失败navigate
            wx.redirectTo({
              url: '/pages/result/result?result=success&msg=XX成功'
            });
          } else {
            wx.navigateTo({
              url: `/pages/result/result?result=fail&msg=${jsonData.errormsg ? jsonData.errormsg : 'XX设置失败'}`
            });
          }
          break;
        default:
          break;
      }

    }
  }

})
```

### 中文处理
在传输有中文的数据时，硬件那边会出现总长度不对的情况。可能是分包导致了编码被分离了，硬件那边也是先转码再判断长度的。解决办法是，讲中文单独分离出一段发送（因为项目中只是姓名长度较短，所以可以一次性发完）。
```javascript
submitEvent (e) {
  // ...
  let sendData = this.JSONtoData(data) // 将所有数据先转成字符串
  let splitData = sendData.split(peopleName) // 以名字进行分割
  let firstData = encodeUtf8(splitData[0]) // 第一段 转码成数组
  let secondData = encodeUtf8(peopleName) // 第二段
  let thirdData = encodeUtf8(splitData[1]) // 第三段
  this.pageData.secondData = secondData
  this.pageData.thirdData = thirdData 

  this.pageData.sendState = 1
  this.BLESendData(firstData) // 发送第一段数据

  // ...
},
BLESendData(code) {
  let sendPackage = code.splice(0, 20)
  let buffer = arrayToBuffer(sendPackage)

  wx.writeBLECharacteristicValue({
    deviceId: app.globalData.device.deviceId,
    serviceId: app.globalData.serviceId,
    // 这里的 characteristicId 需要在 getBLEDeviceCharacteristics 接口中获取
    characteristicId: app.globalData.characteristicWriteId,
    // 这里的value是ArrayBuffer类型
    value: buffer,
    success: () => {
      if (code.length) {
        this.BLESendData(code)
      } else {
        console.log('分段', this.pageData.sendState)
        if (this.pageData.sendState === 1 && this.pageData.secondData.length > 0) {
          // 第一段发完，发第二段
          this.pageData.sendState = 2 // 标记当前发送的分段
          this.BLESendData(this.pageData.secondData)
        } else if (this.pageData.sendState === 2 && this.pageData.thirdData.length > 0) {
           // 第二段发完，发第三段
          this.pageData.sendState = 3
          this.BLESendData(this.pageData.thirdData)
        } else {
          this.pageData.sendState = 0
        }
      }
    },
    complete: (writeRes) => {
      console.log('BLE write complete', writeRes)
    }
  })
}
```

### 图片处理
小程序增加了`getFileSystemManager`的接口，可以读取文件了，喜大普奔。
1. 用`chooseImage`选择或拍摄图片，获取文件的路径和信息；
2. 如果图片小于限定尺寸，直接读取文件编码；如果超出尺寸，先用canvas等比例压缩，`canvasToTempFilePath`保存图片之后，再读取文件编码；
3. `fs.getFileInfo`读取文件的大小；`fs.readFile`读取文件编码，编码`encoding: 'hex'`，得到的文件转码成16进制之后的字符串。

```javascript
chooseImageEvent () {
  wx.chooseImage({
    count: 1,
    sizeType: ['original'],
    sourceType: ['album', 'camera'],
    success: (chooseImageRes) => {
      // tempFilePath可以作为img标签的src属性显示图片
      console.log('choose img', chooseImageRes)
      const tempFilePath = chooseImageRes.tempFilePaths[0]
      wx.getImageInfo({
        src: tempFilePath,
        success: (imageInfo)=>{
          console.log('img info', imageInfo)
          // 获取图片名称
          let pathSplit = imageInfo.path.split('/')
          let fileName = pathSplit[pathSplit.length - 1]
          this.pageData.photoName = fileName
          
          this.compressImg(imageInfo)
        },
        fail: (res) => {
          console.error('getiamgeInfo error', res)
        }
      });
    },
    fail: (res) => {
      console.error('chooseImage fail', res)
    }
  })
},
// 压缩图片
compressImg (imageInfo) {
  wx.showLoading({
    title: '照片压缩中',
    mask: true
  });
  let { width, height, path } = imageInfo
  if (width <= limitLong && height <= limitLong) {
    // 在限制宽高内，直接显示读取
    this.setData({
      previewPhoto: path
    })
    this.readFile(path)
  } else {
    this.setData({
      previewPhoto: ''
    })
    let whRatio = width / height // 计算长宽比
    let cW
    let cH
    if (whRatio > 1) {
      // 横向，且宽度超出，宽度设置为限定宽，高度按比例缩小
      cW = limitLong
      cH = height * (limitLong / width )
    
    } else {
      cW = width * (limitLong / height)
      cH = limitLong
    }
    this.setData({
      canvasWidth: cW,
      canvasHeight: cH
    })
    this.drawImage(path, cW, cH)      
  }
},
drawImage (path, width, height) {
  let ctx = wx.createCanvasContext('previewCanvas')
  ctx.drawImage(path, 0, 0, width, height)
  setTimeout(() => {
    ctx.draw(false, () => {
      wx.canvasToTempFilePath({
        destWidth: width,
        destHeight: height,
        canvasId: 'previewCanvas',
        fileType: 'jpg',
        quality: 0.8,
        success: (result) => {
          this.readFile(result.tempFilePath)
          this.setData({
            previewPhoto: result.tempFilePath
          })
        },
        fail: (res) => {
          wx.hideLoading()
          console.error('canvasToTempFilePath fail', res)
        }
      }, this);
    })
  }, 500)
},
readFile (filePath) {
  let fs = wx.getFileSystemManager()
  fs.access({
    path: filePath,
    success: () => {
      fs.getFileInfo({
        filePath,
        success: (res) => {
          console.log('图片信息', res)
          let size = res.size
          this.pageData.photoSize = size
        },
        fail: (res) => {
          console.error('getFileInfo fail', res)
          Notify({
            text: '读取图片信息失败',
            duration: 2000
          });
        }
      })

      fs.readFile({
        filePath,
        encoding: 'hex',
        success: (res) => {
          console.log('图片编码', res.data.length, res)
          let fileData = res.data
          this.pageData.photoData = fileData
        },
        fail: (res) => {
          console.error('readFile fail', res)
          Notify({
            text: '读取图片数据失败',
            duration: 2000
          });
        }
      })
    },
    fail: () => {
      Notify({
        text: '文件或者目录不存在',
        duration: 2000
      });
    },
    complete: () => {
      wx.hideLoading()
    }
  })
},
```

### 进度条
由于数据量大，需要一个进度条展示。
```javascript
submitEvent (e) {
  // ...
  // sendData是数据数组的总长度，计算要发送多少次
  let totalTimes = Math.floor(sendData.length / 20) // 总次数 = 总长度 / 20
    
  this.pageData.step = 100 / totalTimes // 进度条分为100分，计算每次步进
  // ...
},
BLESendData(code) {
  if (this.data.progress <= 99) {
    // 每发送一次数据，增加进度。由于没那么精确，100的情况放在接收数据完成时处理
    this.setData({
      progress: this.data.progress + this.pageData.step
    })
  }
}
```

## 表单验证
表单验证使用了别人的轮子[WxValidate](https://github.com/skyvow/wx-extend/blob/master/docs/components/validate.md)。`new WxValidate(rule, message)`初始化，使用`addMethod`方法添加自定义表单规则。

```javascript
// 验证规则
const rules = {
  deviceIp: {
    required: true,
    ip: true, // ip是自定义规则
  }
}

// 验证字段的提示信息，若不传则调用默认的信息
const messages = {
  deviceIp: {
    required: '请输入IP地址',
    ip: '请输入正确的IP地址'
  }
}

Page({
  onLoad () {
    // 在页面加载后初始化表单验证
    this.initValidate()
  },
  initValidate() {
    this.WxValidate = new WxValidate(rules, messages)
    // 自定义验证规则
    this.WxValidate.addMethod('ip', (value, param) => {
      return this.WxValidate.optional(value) || /^(2(5[0-5]{1}|[0-4]\d{1})|[0-1]?\d{1,2})(\.(2(5[0-5]{1}|[0-4]\d{1})|[0-1]?\d{1,2})){3}$/g.test(value)
    }, '请填写正确的地址')
  },
  // 表单提交事件
  submitEvent(e) {
    const params = e.detail.value
    if (!this.WxValidate.checkForm(params)) {
      // 验证不通过
      const error = this.WxValidate.errorList[0] // 第一个错误提示
      Notify({
        text: error.msg,
        duration: 2000
      });
      return false
    } else {
      // 验证通过
    }
  }
})
```

## bug
### 多次传输图片之后无法再从菜单进入配置页
页面跳转流程是这样的：
{% asset_img route.jpeg 路由流程 %}

每次result页面`redirectTo`menus页面时，都会将menus页推入路由栈中，然后导致路由栈满了，页面没法跳转到其他页面。

解决办法是，使用`navigateBack`到menus页，这样就不会增加路由栈的长度，而是重新打开menus页