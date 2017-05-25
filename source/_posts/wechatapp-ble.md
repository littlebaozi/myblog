---
title: 微信小程序 - 蓝牙接口
date: 2017-05-23 11:00:08
thumnail: http://pic.baike.soso.com/pqpic/baikepic/34576/cut-20140506154508-492844147.jpg/0
tags: 
- 微信小程序
categories: 
- 框架
---

　　小程序的功能越来越多了，特别是调用硬件的能力，完全可以有APP的能力。现在蓝牙功能开放了，正好项目上有蓝牙开门的需求，挖坑开始。
　　做网页接触硬件的机会是比较少的，一开始也是一头雾水。小程序的蓝牙是基于蓝牙4.0的，所以需要支持蓝牙4.0的设备。

## 一、蓝牙4.0
　　先得了解一点蓝牙4.0方面的姿势：
> 蓝牙4.0是协议，4.0是协议版本号，蓝牙4.0是2010年6月由SIG（Special Interest Group）发布的蓝牙标准，它有两种模式：
> * 1、BLE（Bluetooth low energy）只能与4.0协议设备通信，适应节能且仅收发少量数据的设备（如手环、智能体温计），称之为低功耗蓝牙；
> * 2、BR/EDR（Basic Rate / Enhanced Data Rate），向下兼容（能与3.0/2.1/2.0通信），适应收发数据较多的设备（如耳机、手机、平板）。这个模式常常也有人称之为“传统蓝牙”或“经典蓝牙”；

> <p>可以这样理解，蓝牙4.0协议包含BLE，BLE隶属于蓝牙4.0协议的一部分。<p>
> <p>BLE技术是基于GATT进行通信的，GATT是一种属性传输协议，简单的讲可以认为是一种属性传输的应用层协议。</p>
> <p>每一个设备中存在很多的“service”(服务)，service中还包含有多个“Characteristic”（特征值），每个Characteristic具备不同的权限（读、写、通知）。</p>

<!--more-->

{% asset_img gatt.jpg GATT %}

关于BLE相关的知识可以看这篇：[GATT Profile 简介](https://www.race604.com/gatt-profile-intro/)。

和蓝牙设备通信都是对characteristic进行操作的，主要包括：
* 读取特征值
* 写入特征值
* 特征值变化通知

所以，开发的时候，需要向硬件开发人员了解设备的相关server和characteristic。连接设备可以通过MAC地址（Android）、uuid（iOS）、设备名称。

兼容性：基础库1.1.0，iOS 微信客户端 6.5.6 版本开始支持，Android 6.5.7 版本开始支持

## 二、连接流程
流程图

　　在项目中，已知设备的MAC地址（Android作为deviceId）、service的uuid、写入 （需要发送一次地址匹配命令包、一次开门命令包）characteristic的uuid、notify（获取门禁回复的命令包）characteristic的uuid。

### 1. 初始化蓝牙
　　小程序是没有打开蓝牙权限的，所以用户没有打开蓝牙需要提示他打开。本来想用[`wx.getBluetoothAdapterState`](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxgetbluetoothadapterstateobject)的`res.adapterState.available`来判断是否打开，但是首次打开蓝牙之后，再关闭，available一直是true。所以，还是直接使用[`wx.openBluetoothAdapter`](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxopenbluetoothadapterobject)来判断，success表明已打开，fail表明未打开。

### 2. 连接蓝牙过程
　　Android可以使用deviceId（项目中是MAC地址）直接发起连接；iOS只能连接已发现的设备（即已知的deviceId必须在接口wx.getBluetoothDevices的返回结果中存在），且获取的是设备uuid，只能全部连接一遍发送数据（因为会发送一次设备MAC地址的匹配命令包，所以同时有多个设备只会打开一个）。

#### 2.1 搜索蓝牙设备（IOS），[wx.startBluetoothDevicesDiscovery](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxstartbluetoothdevicesdiscoveryobject)
　　可以使用参数services（蓝牙设备主 service 的 uuid 列表）, 筛选设备。

#### 2.2 监听寻找到新设备（IOS），[wx.onBluetoothDeviceFound](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxonbluetoothdevicefoundcallback)
　　在成功回调中使用`wx.getBluetoothDevices`获取设备 -> 成功回调中循环devices -> 判断device的uuid是否有连接的service uuid（上一步使用了services参数，就不需要判断了），有则使用设备uuid做deviceId连接设备。

#### 2.3 连接蓝牙设备，[wx.createBLEConnection](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxcreatebleconnectionobject)
　　 在success的回调中：
1. Android直接调用`wx.writeBLECharacteristicValue`写入数据（写在getBLEDeviceCharacteristics成功回调中会报10008错 
2. `wx.getBLEDeviceServices`获取已发现的设备列表
3. 成功回调中`wx.getBLEDeviceCharacteristics`获取指定service的特征值
4. 成功回调中，写入数据（iOS），开启特征值变化通知`wx.notifyBLECharacteristicValueChange`（需要写在这里，写在getBLEDeviceServices之前会报错）-> ` wx.onBLECharacteristicValueChange`

#### 2.4 写入数据，[writeBLECharacteristicValue](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxwriteblecharacteristicvalueobject)
　　参数value的类型是ArrayBuffer（蓝牙设备特征值对应的二进制值）。ArrayBuffer是什么？

> ArrayBuffer对象被用来表示一个通用的，固定长度的二进制数据缓冲区。

官方术语比较难理解，可以理解为存储二进制数据的对象。详细解释可以看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer),还有一篇通俗点的解释[理解DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型](http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)。
　　官方例子中使用`ArrayBuffer`创建一个字节长度的`ArrayBuffer`对象，然后使用DataView读入数据。
　　`ArrayBuffer`是不能读写的，需要使用[`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView)或者[类型化数组](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Typed_arrays)来读写。
```javascript
// 向蓝牙设备发送一个0x00的16进制数据
let buffer = new ArrayBuffer(1)
let dataView = new DataView(buffer)
dataView.setUint8(0, 0)
```
　　在项目中，需要传一个13长度的数组命令包，数组中每个元素代表不同命令。类型化数组有大部分数组的方法，不过没有concat、splice方法，而创建类型化数组可以直接传入数组。
1. 用数组创建，元素用2位16进制表示，比如`let arr = [0x01, 0xaa]`；
2. 拼接、插入处理数组；
3. `let bufferView = new Uint8Array(arr)`将数组转换为8位无符号整型数组吗，就是说长度为arr长度，每个元素是8位大小。为什么用8位呢？ 因为8位二进制正好可以表示2位16进制。8位二进制`0000 0000~ 1111 1111`，2位16进制`0x00~0xFF`，表示的大小范围相同10进制都是0~255。
4. `bufferView.buffer`转换为ArrayBuffer。buffer可以返回由 Uint8Array引用的 ArrayBuffer。
5. 前面已经监听了特征值变化，所以发送数据后会有notify返回，里面有设备返回的数据。返回的数据也是ArrayBuffer，所以先用`Uint8Array`转一下，使用下面的方法转车16进制。
```javascript
Array.prototype.slice.call(decodeDataView).map((_v) => {
    return _v.toString(16)
})
```


## 三、iOS和Android接口使用差异
* IOS使用蓝牙设备UUID作为deviceId连接设备，Android使用蓝牙设备的MAC地址作为deviceId连接设备。
* iOS的UUID只支持大写，Android大小写都行。

## 四、坑
　　附上网友总结的坑[跳坑《一百七十六》蓝牙API使用指南](http://www.wxapp-union.com/forum.php?mod=viewthread&tid=4052&highlight=%E8%93%9D%E7%89%99)
<p> Q: Android系统获取的service，只有1800和1801两个。</p>
<p> A: 微信版本要6.5.8以上，卸载微信，重新安装。</p>

<p> Q: 获取的蓝牙MAC地址顺序是反的。</p>
<p> A: 同事说是大端小端的问题，看这篇[大端小端格式详解](http://blog.csdn.net/zhaoshuzhaoshu/article/details/37600857/)</p>
