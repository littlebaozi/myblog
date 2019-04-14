---
title: 微信小程序 - 蓝牙接口
thumbnail: https://pic.baike.soso.com/pqpic/baikepic/34576/cut-20140506154508-492844147.jpg/0
date: 2017-05-23 11:00:08
tags: 
- 小程序
categories: 
- 开发
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

兼容性：基础库1.1.0，iOS 微信客户端 6.5.6 版本开始支持，Android 6.5.7 版本开始支持

## 一、接口
### 1. wx.getBluetoothAdapterState
#### 1.1 未调用过`openBluetoothAdapter`
* 蓝牙打开/未打开状态

fail，接口调用失败
```json
{errCode: 10000, errMsg: "getBluetoothAdapterState:fail:not init"}
```
#### 1.2 调用过`openBluetoothAdapter`
* 蓝牙打开状态

success，接口调用成功
```json
{discovering: false, errMsg: "getBluetoothAdapterState:ok", available: true}
```

* 蓝牙未打开状态
success，接口调用成功
```json
{discovering: false, errMsg: "getBluetoothAdapterState:ok", available: false}
```


## 二、连接流程
主要的流程如下：
初始化蓝牙适配器（openBluetoothAdapter） ->  查找蓝牙设备（startBluetoothDevicesDiscovery）-> 获取设备（getBluetoothDevices） -> 连接设备（createBLEConnection） -> 获取设备服务（getBLEDeviceServices） -> 获取设备特征值（getBLEDeviceCharacteristics） -> 开启蓝牙设备通知（notifyBLECharacteristicValueChange） -> 写入数据（writeBLECharacteristicValue） -> 关闭蓝牙连接（closeBLEConnection）

下图是公司项目中的流程，由于Android和iOS的接口差异和项目需求，所以搞得挺麻烦的。

{% asset_img bleflow.jpeg 蓝牙设备交互流程图 %}

　　对蓝牙设备的读写操作主要是通过对应特征值的操作，在项目中获取到的设备特征值有三个：write、read、notify，其中write、notify是true，表示可以使用相应接口做操作。具体要看自己的项目中蓝牙设备的情况使用。
　　这个项目中，是用notify来监听设备返回数据的，所以不用手动去read操作。因此过程是不一样的，开启设备通知 -> 写入设备的匹配命令 -> 设备返回成功命令 -> 发送操作命令 -> 设备返回成功命令

　　在项目中，已知设备的MAC地址。Android可以直接使用MAC地址发起连接，createBLEConnection的deviceID就是蓝牙设备的MAC地址。

　　但是ios不一样，deviceID是蓝牙设备的UUID，事先是不知道的，只有对已发现的设备全部连接一遍做一次设备匹配才能确定哪个才是要连接的设备。所以iOS的连接操作是在`wx.onBluetoothDeviceFound`中进行的。每次发现一个设备，就进行一次连接匹配，直到匹配到需要连接的设备后停止设备搜索。（已发现设备会缓存起来，所以我是每次用户操作时都会closeBluetoothAdapter，清空数据）

　　但是问题又来了，发现设备的间隔是很快的，可能两次设备连接同时进行，然后互相产生了干扰导致连接经常失败。我就将发现的设备存入数组中，每次发现设备就push进去。从数组第一个开始连接，连接失败就在数组中移除，然后连接下一个，直到连接到正确的设备。

### 1. 初始化蓝牙
　　小程序是没有打开蓝牙权限的，所以用户没有打开蓝牙需要提示他打开。本来想用[`wx.getBluetoothAdapterState`](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxgetbluetoothadapterstateobject)的`res.adapterState.available`来判断是否打开，但是首次打开蓝牙之后，再关闭，available一直是true。所以，还是直接使用[`wx.openBluetoothAdapter`](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxopenbluetoothadapterobject)来判断，success表明已打开，fail表明未打开。

### 2. 连接蓝牙的主要操作
　　Android可以使用`deviceId`（项目中是MAC地址）直接发起连接；
　　iOS只能连接已发现的设备（即已知的deviceId必须在接口`wx.getBluetoothDevices`的返回结果中存在），且获取的是设备uuid，项目中只事先知道设备MAC地址，只能全部连接一次发送地址匹配的命令，匹配通过才发送开门命令。

#### 2.1 搜索蓝牙设备，[wx.startBluetoothDevicesDiscovery](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxstartbluetoothdevicesdiscoveryobject)
　　可以设置参数`services`（蓝牙设备主 service 的 uuid 列表）, 只查找有指定service uuid的蓝牙设备。
　　在成功回调中，Android直接发起连接，iOS则监听设备发现。

#### 2.2 监听寻找到新设备（IOS），[wx.onBluetoothDeviceFound](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxonbluetoothdevicefoundcallback)
　　每次监听到新设备，就发起一次设备连接。

#### 2.3 连接蓝牙设备，[wx.createBLEConnection](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxcreatebleconnectionobject)
　　 在连接success的回调中：
1. Android直接调用`wx.writeBLECharacteristicValue`写入数据（写在`getBLEDeviceCharacteristics`成功回调中，第二次发送数据会报10008错，不知道原因）；
2. `wx.getBLEDeviceServices`获取已发现的设备列表；
3. 成功回调中`wx.getBLEDeviceCharacteristics`获取指定service的特征值；
4. 成功回调中，写入数据（iOS），开启特征值变化通知`wx.notifyBLECharacteristicValueChange`
5. 成功回调中，使用`wx.onBLECharacteristicValueChange`监听特征值变化，然后发送地址匹配命令数据包（Android需要做一个延时发送数据，否则写入数据会报10008错误）；

#### 2.4 写入数据，[writeBLECharacteristicValue](https://mp.weixin.qq.com/debug/wxadoc/dev/api/bluetooth.html#wxwriteblecharacteristicvalueobject)
　　参数value的类型是`ArrayBuffer`（蓝牙设备特征值对应的二进制值）。那`ArrayBuffer`是什么？

> `ArrayBuffer`对象被用来表示一个通用的，固定长度的二进制数据缓冲区。

官方术语比较难理解，可以理解为存储二进制数据的对象。详细解释可以看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer),还有一篇通俗点的解释[理解DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型](http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)。
　　`ArrayBuffer`是不能直接读写的，需要使用[`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView)或者[类型化数组,`Uint8Array`等](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Typed_arrays)来读写。

```javascript
// 向蓝牙设备发送一个0x00的16进制数据
let buffer = new ArrayBuffer(1)·
let dataView = new DataView(buffer)
dataView.setUint8(0, 0)
```

　　在项目中，需要传一个固定长度的数据命令包，每一位代表不同命令，用两位16进制表示。类型化数组有大部分数组的方法，不过没有concat、splice方法。创建类型化数组时可以直接传入数组转化。
1. 先创建一个数组，存储需要发送的数据，元素用2位16进制表示，比如`let arr = [0x01, 0xaa]`；
2. `let bufferView = new Uint8Array(arr)`将数组转换为8位无符号整型数组，就是说长度为arr.length，每个元素是8位大小。为什么用8位呢？ 因为8位二进制正好可以表示2位16进制。8位二进制`0000 0000~ 1111 1111`，2位16进制`0x00~0xFF`，表示的大小范围相同10进制都是0~255。
3. `bufferView.buffer`转换为`ArrayBuffer` 。`buffer`可以返回由`Uint8Array`引用的`ArrayBuffer`。
4. 前面已经监听了特征值变化，所以发送数据后会有notify返回，里面有设备返回的数据。返回的数据也是`ArrayBuffer`，所以先用`Uint8Array`转一下，就可以取到数组中的值。想要更直观的查看里面的16进制值，可以使用下面的方式处理：
```javascript
Array.prototype.slice.call(decodeDataView).map((_v) => {
    return _v.toString(16)
})
```

　　也可以先使用`var buffer = new ArrayBuffer(arr)`，然后用`new Uint8Array(buffer)`创建视图增删改里面的数据，

## 三、iOS和Android接口使用差异
* IOS使用蓝牙设备UUID作为deviceId连接设备，Android使用蓝牙设备的MAC地址作为deviceId连接设备。
* iOS的UUID只支持大写，Android大小写都行。

## 四、坑
　　附上网友总结的坑[跳坑《一百七十六》蓝牙API使用指南](http://www.wxapp-union.com/forum.php?mod=viewthread&tid=4052&highlight=%E8%93%9D%E7%89%99)
<p> Q: Android系统获取的service，只有1800和1801两个。</p>
<p> A: 微信版本要6.5.8以上，卸载微信，重新安装。</p>

<p> Q: 获取的蓝牙MAC地址顺序是反的。</p>
<p> A: 同事说是大端小端的问题，看这篇[大端小端格式详解](http://blog.csdn.net/zhaoshuzhaoshu/article/details/37600857/)</p>
