---
title: 前端跳槽面试必备技巧
date: 2020-06-03 21:50:29
tags: 面试
category: 学习
---

# 1 页面布局
## 1.1 问题：高度已知，三栏布局，左右栏宽度都为300px，中间自适应
### 布局方式

<script async src="//jsfiddle.net/littlebaozi/qnxagtsb/embed/html,css,result/"></script>

* float
  * 优点：兼容性好
  * 缺点：浮动会脱离文档流，会影响`.layout`的兄弟元素，需要做好清除浮动；`.center`高度比两边高时，内容会流动到两边；`.layout`的高度同`.center`高度，`side高度`可能会超出`.layout`

* 绝对定位
  * 优点：兼容性好，布局快捷
  * 缺点：元素脱离文档流，`.layout`高度不能撑开

* table
  * 优点：兼容性很好
  * 缺点：高度会同时变化，语义化问题

* flex
  * 优点：CSS3的布局方式，目前比较完美的方案
  * 缺点：兼容性

* grid
  * 优点：可以做更复杂布局
  * 缺点：兼容性还不够好

### 扩展问题
* float布局为什么`.center`要放最后？
  左侧设置`float：left`，脱离文档流，右侧则会到第一行，中间到第二行；右侧设置`float: right`，脱离文档流，中间到第一行。

* 清除浮动的几种方式？
  * 空元素`clear:both`
  * 伪元素`clear:both`
  * BFC，`overflow`非`visible`

* 高度不确定时，哪些适用？中间元素高度增加，左右元素能自适应增加高度吗？
  左右不设置高度的情况下，**table布局、flex布局**可以。

* 最优方案
  易用性、兼容性来讲，**flex布局方案**最好。

* 其他布局
  * 三栏布局
    * 左右宽度固定，中间自适应
    * 上下高度固定，中间自适应
  * 两栏布局
    * 左宽度固定，右自适应
    * 右宽度固定，左自适应
    * 上高度固定，下自适应
    * 下高度固定，上自适应
### 小结
* 语义化掌握到位 article section 不要通篇 div
* 页面布局理解深刻
* CSS 基础知识扎实
* 思维灵活积极向上 知道每个方案的优缺点、对比
* 代码书写规范

# 2. CSS盒模型
## 2.1 盒模型
### 概念
盒模型分为标准盒模型(content-box)和IE盒模型(border-box)。

### 区别
* 标准盒模型，元素宽/高是content区域的宽/高
{% asset_img content-box.png content-box %}

* IE盒模型，元素宽/高是content宽/高 + padding + border
{% asset_img border-box.png border-box %}

### 如何设置
`box-sizing: content-box | border-box`

### js获取盒子宽度
* `dom.style.width/height` 只能获取内联样式
* `window.getComputedStyle(dom).width/height` 只读，IE8以下不支持
* `dom.getBoundingClientStyle()`IE8以下不支持
  * `content-box` 值：width + padding + border
  * `border-box` 值：width
* `dom.currentStyle`IE自有

## 2.2 BFC