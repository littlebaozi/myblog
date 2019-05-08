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
初始化蓝牙适配器（openBluetoothAdapter） ->  查找蓝牙设备（startBluetoothDevicesDiscovery） -> 找到设备停止搜索（stopBluetoothDevicesDiscovery）-> 连接设备（createBLEConnection） -> 获取设备服务（getBLEDeviceServices） -> 获取设备特征值（getBLEDeviceCharacteristics） -> 开启蓝牙设备通知（notifyBLECharacteristicValueChange）-> 监听蓝牙通知，返回数据的处理逻辑（onBLECharacteristicValueChange） -> 写入数据（writeBLECharacteristicValue） -> 关闭蓝牙连接（closeBLEConnection）或关闭蓝牙适配器（closeBluetoothAdapter）

### 1. 初始化蓝牙
　　小程序是没有打开蓝牙权限的，所以用户没有打开蓝牙需要提示他打开。本来想用wx.getBluetoothAdapterState的`res.adapterState.available`来判断是否打开，但是首次打开蓝牙之后，再关闭，available一直是true（后来的项目中发现没这个问题了，大概是修复了）。而且如果没有`wx.openBluetoothAdapter`成功过，`wx.getBluetoothAdapterState`是走fail回调的。所以，还是直接使用wx.openBluetoothAdapter来判断，success表明已打开，fail表明未打开。并且重新搜索操作设备最好还是先`wx.closeBluetoothAdapter`。

### 2. 连接蓝牙的主要操作
　　Android可以使用`deviceId`（设备的MAC地址）直接发起连接；
　　iOS只能连接已发现的设备（即已知的deviceId必须在接口`wx.getBluetoothDevices`的返回结果中存在），且获取的是设备uuid，有个项目中只事先知道设备MAC地址，只能全部连接一次发送设备匹配的命令，匹配通过才能继续发送操作命令。

#### 2.1 搜索蓝牙设备，wx.startBluetoothDevicesDiscovery
　　有的设备会广播自己的service ID，可以设置参数`services`（蓝牙设备主 service 的 uuid 列表）, 直接查找需要的设备。

#### 2.2 监听寻找到新设备，wx.onBluetoothDeviceFound
　　每次监听到新设备，就发起一次设备连接。
　　不同的项目对找到的项目可以有不同的处理方式。
* 如果是能列出搜索到的设备，则可以在回调中，根据设备RSSI进行排序，将信号强（意味着最近）的设备排最开始。用户点击哪个设备就对哪个设备发起连接。
* 有的设备对用户是有操作权限的。所以一开始用户能操作的设备会有一个列表。当用户操作设备，并搜索到多个设备的时候，我们也无法知道哪个设备是用户要操作的。
    * 有个项目是发送设备mac地址去做校验的。这就需要将搜索到设备做一次遍历连接发送数据校验。
    * 有个项目什么都不知道，也不列出搜索到的设备，只能连接第一个发现的设备（也可以连接一定时间内找到的信号最强的设备）

#### 2.3 连接蓝牙设备，wx.createBLEConnection
　　在某个项目中，已知设备的MAC地址。Android可以直接使用MAC地址发起连接，createBLEConnection的deviceID就是蓝牙设备的MAC地址；但是ios不一样，deviceID是蓝牙设备的UUID，事先是不知道的。

### 3. 数据交互

#### 3.1 获取设备服务
* 要发送数据监听通知需要先获取服务和特征值。简单点就直接遍历`getBLEDeviceServices`和`getBLEDeviceCharacteristics`获取一下。一般是硬件会告诉你用到的UUID，所以可以直接用。
* 先要开启特征值变化通知`wx.notifyBLECharacteristicValueChange`。然后使用`wx.onBLECharacteristicValueChange`监听特征值变化。发送数据后设备返回数据在这里监听，逻辑处理也是写在这里的。
* Android最好做一个延时发送数据，否则写入数据会报10008错误。

#### 3.2 写入数据，writeBLECharacteristicValue
　　参数value的类型是`ArrayBuffer`（蓝牙设备特征值对应的二进制值）。那`ArrayBuffer`是什么？

> `ArrayBuffer`对象被用来表示一个通用的，固定长度的二进制数据缓冲区。`ArrayBuffer`不能直接操作，而是要通过[类型化数组,`Uint8Array`等](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Typed_arrays)或[`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView)对象来操作，它们会将缓冲区中的数据表示为特定的格式，并通过这些格式来读写缓冲区的内容。

> `DataView` 视图是一个可以从 `ArrayBuffer` 对象中读写多种数值类型的底层接口，使用它时，不用考虑不同平台的字节序问题

> `Uint8Array` 数组类型表示一个8位无符号整型数组，创建时内容被初始化为0。创建完后，可以以对象的方式或使用数组下标索引的方式引用数组中的元素。

参考资料[理解DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型](http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)。

反正`ArrayBuffer`就是用来存储二进制数据的。而想要用js读写二进制数据则需要类型化数组。

操作二进制数据用两种
* 1. 使用DataView。
```javascript
// 向蓝牙设备发送一个0x00的16进制数据
let buffer = new ArrayBuffer(1) // 创建一个1个字节的缓冲区
let dataView = new DataView(buffer) // 用DataView来视图化buffer
dataView.setUint8(0, 2) // 在0处存储2（一个字节大小）
```

* 2. 使用类型化数组
```javascript
let dataView = new Uint8Array([21,31]);
console.log(dataView[1]); // 31
let buffer = dataView.buffer // 获取buffer二进制数据
```

不同项目会有不同的数据发送格式。有的有16进制的命令串，有的用json。

蓝牙收发数据有20字节的限制，所以需要做分包发送和分包接收处理。小程序里只能手动分割数据，依次调用`wx.writeBLECharacteristicValue`发送数据给设备。

##### 3.2.1 16进制命令形式
　　需要传一个固定长度的数据命令包，每一位代表不同命令，用两位16进制表示。
1. 先创建一个数组，存储需要发送的数据，元素用2位16进制表示，比如
```javascript
let arr = [0x01, 0xAA]
```

2. 将数组转换为8位无符号整型数组。长度为arr.length，每个元素是8位大小。
为什么用8位呢？ 因为8位二进制正好可以表示2位16进制。8位二进制`0000 0000 ~ 1111 1111`，2位16进制`0x00 ~ 0xFF`，表示的都是10进制0~255。（类型化数组有大部分数组的方法，不过没有concat、splice方法。创建类型化数组时可以直接传入数组转化。）
```javascript
let bufferView = new Uint8Array(arr)
```
或者用DataView，但不如上一种方法方便
```javascript
let ab = new ArrayBuffer(arr.length)
let dv = new DataView(ab)
for (let i = 0; i < arr.length; i++) {
    dv.setUint8(i, arr[i], false) // false表示大端写入，实际上不用写
}
```
3. `bufferView.buffer`可以获取`ArrayBuffer` 。
4. 前面已经监听了特征值变化，所以发送数据后设备会返回数据。返回的数据也是`ArrayBuffer`，所以先用`Uint8Array`转一下，就可以取到数组中的值。
```javascript
let dataView = new Uint8Array(buffer); // 这里的buffer是onBLECharacteristicValueChange回调中获取的
```
如果是用DataView，则可以用下面的方式：
```javascript
let dv = new DataView(buffer)
let arr = []
for (let i = 0; i < (new Uint8Array(buffer)).length; i++) {
    arr[i] = dv.getUint8(i, false) // false表示大端读取，实际上不用写
}
```
想要更直观的查看里面的16进制值，可以使用下面的方式处理：
```javascript
Array.prototype.slice.call(dataView).map((_v) => {
    return _v.toString(16)
})
```

##### 3.2.2 JSON字符串
有个项目是要求发送JSON字符串形式的数据。JSON数据转字符串直接用`JSON.stringify()`转。相对地，用`JSON.parse()`转回JSON。主要字符串转二进制有点麻烦。硬件要求转`UTF-8`，如果不涉及中文，转`Unicode`也行。关于unicode和utf-8的知识就不展开了，网上有很多相关文章。
* 如果不涉及中文，可以直接转`Unicode`会很方便，js的`charCodeAt`直接就能获取编码
```javascript
// 字符串转unicode数组
str.split('').map((v) => v.charCodeAt(0))

// unicode转字符串
String.fromCharCode(...arr)
```
* 如果涉及中文，`Unicode`的编码就不能用了，需要转`UTF-8`。下面是网上找到代码。
```javascript
/**
 * Make sure the charset of the page using this script is
 * set to utf-8 or you will not get the correct results.
 */
let highSurrogateMin = 0xd800,
    highSurrogateMax = 0xdbff,
    lowSurrogateMin = 0xdc00,
    lowSurrogateMax = 0xdfff,
    surrogateBase = 0x10000;

function isHighSurrogate(charCode) {
    return highSurrogateMin <= charCode && charCode <= highSurrogateMax;
}

function isLowSurrogate(charCode) {
    return lowSurrogateMin <= charCode && charCode <= lowSurrogateMax;
}

function combineSurrogate(high, low) {
    return ((high - highSurrogateMin) << 10) + (low - lowSurrogateMin) + surrogateBase;
}

/**
 * Convert charCode to JavaScript String
 * handling UTF16 surrogate pair
 */
function chr(charCode) {
    let high, low;

    if (charCode < surrogateBase) {
        return String.fromCharCode(charCode);
    }

    // convert to UTF16 surrogate pair
    high = ((charCode - surrogateBase) >> 10) + highSurrogateMin,
        low = (charCode & 0x3ff) + lowSurrogateMin;

    return String.fromCharCode(high, low);
}

/**
 * Convert JavaScript String to an Array of
 * UTF8 bytes
 * @export
 */
function stringToBytes(str) {
    let bytes = [],
        strLength = str.length,
        strIndex = 0,
        charCode, charCode2;

    while (strIndex < strLength) {
        charCode = str.charCodeAt(strIndex++);

        // handle surrogate pair
        if (isHighSurrogate(charCode)) {
            if (strIndex === strLength) {
                throw new Error('Invalid format');
            }

            charCode2 = str.charCodeAt(strIndex++);

            if (!isLowSurrogate(charCode2)) {
                throw new Error('Invalid format');
            }

            charCode = combineSurrogate(charCode, charCode2);
        }

        // convert charCode to UTF8 bytes
        if (charCode < 0x80) {
            // one byte
            bytes.push(charCode);
        } else if (charCode < 0x800) {
            // two bytes
            bytes.push(0xc0 | (charCode >> 6));
            bytes.push(0x80 | (charCode & 0x3f));
        } else if (charCode < 0x10000) {
            // three bytes
            bytes.push(0xe0 | (charCode >> 12));
            bytes.push(0x80 | ((charCode >> 6) & 0x3f));
            bytes.push(0x80 | (charCode & 0x3f));
        } else {
            // four bytes
            bytes.push(0xf0 | (charCode >> 18));
            bytes.push(0x80 | ((charCode >> 12) & 0x3f));
            bytes.push(0x80 | ((charCode >> 6) & 0x3f));
            bytes.push(0x80 | (charCode & 0x3f));
        }
    }

    return bytes;
}

/**
 * Convert an Array of UTF8 bytes to
 * a JavaScript String
 * @export
 */
function bytesToString(bytes) {
    let str = '',
        length = bytes.length,
        index = 0,
        byte,
        charCode;

    while (index < length) {
        // first byte
        byte = bytes[index++];

        if (byte < 0x80) {
            // one byte
            charCode = byte;
        } else if ((byte >> 5) === 0x06) {
            // two bytes
            charCode = ((byte & 0x1f) << 6) | (bytes[index++] & 0x3f);
        } else if ((byte >> 4) === 0x0e) {
            // three bytes
            charCode = ((byte & 0x0f) << 12) | ((bytes[index++] & 0x3f) << 6) | (bytes[index++] & 0x3f);
        } else {
            // four bytes
            charCode = ((byte & 0x07) << 18) | ((bytes[index++] & 0x3f) << 12) | ((bytes[index++] & 0x3f) << 6) | (bytes[index++] & 0x3f);
        }

        str += chr(charCode);
    }

    return str;
}
export {
    stringToBytes,
    bytesToString
};

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
