---
title: 蓝牙门禁配置微信小程序项目总结
date: 2019-11-18 15:56:31
tags: 
  - 小程序
  - 蓝牙
category: 开发
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
        let route = currentPages[currentPages.length - 1].route

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

### 图片传输

### 中文处理

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