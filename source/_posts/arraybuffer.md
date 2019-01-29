---
title: 二进制数组
date: 2019-01-29 16:40:27
tags:
---

1. 先创建一个数组，存储需要发送的数据，元素用2位16进制表示，比如`let arr = [0x01, 0xaa]`；
2. `let bufferView = new Uint8Array(arr)`将数组转换为8位无符号整型数组，就是说长度为arr.length，每个元素是8位大小。为什么用8位呢？ 因为8位二进制正好可以表示2位16进制。8位二进制`0000 0000~ 1111 1111`，2位16进制`0x00~0xFF`，表示的大小范围相同10进制都是0~255。
3. `bufferView.buffer`转换为`ArrayBuffer` 。`buffer`可以返回由`Uint8Array`引用的`ArrayBuffer`。

## 二进制数组
1. ArrayBuffer
表示内存中的一段二进制数据。不能直接读写
2. TypedArray

3. DataView