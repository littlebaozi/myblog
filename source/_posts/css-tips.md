---
title: CSS技巧记录
date: 2018-11-29 21:24:14
tags: CSS
categories: A常用
---

## 布局
* flex左右布局，右侧内容溢出，右侧样式：`flex: 1;min-width: 0;`

* 多行省略号
```css
element{
 display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3; //修改该数字
overflow: hidden;   
}
```
* 单行省略号
```css
element {
    white-space: nowrap;
	overflow: hidden;
    text-overflow:ellipsis;
}
```

## 字体
* 移动端字体
```css
body {
    font-family: -apple-system, BlinkMacSystemFont, "PingFang SC","Helvetica Neue",STHeiti,"Microsoft Yahei",Tahoma,Simsun,sans-serif;
}
```

## bug
* android 4.3 横竖屏，花屏  css设置了 position:relative的问题
{% asset_img android4.3relativebug.jpg 横竖屏花屏 %}
