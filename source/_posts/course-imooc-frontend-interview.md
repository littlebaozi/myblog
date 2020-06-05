---
title: 前端跳槽面试必备技巧
date: 2020-06-03 21:50:29
tags: 面试
category: 学习
---

# 页面布局
## 问题：高度已知，三栏布局，左右栏宽度都为300px，中间自适应
### 布局方式
```css
/* 通用样式 */
* {
  padding: 0;
  margin: 0;
}
.layout{
  background-color: red;
}
.side {
  width: 200px;
  height: 50px;
}
.left {
  background-color: #3498DB;
}
.center {
  background-color: #58D68D;
  height: 60px;
  color: #fff;
  text-align: center;
}
.right {
  background-color: #F4D03F;
}
```
* 浮动
```html
<style>
.layout-float {
  .left{
    float: left;
  }
  .right {
    float: right;
  }
}
</style>
<section class="layout layout-float">
  <div class="side left"></div>
  <div class="side right"></div>
  <div class="center">float布局</div>
</section>
```
  * 优点：兼容性好
  * 缺点：浮动会脱离文档流，需要做好清除浮动；`.center`高度比两边高时，内容会流动到两边；`.center`高度比两边低时，`.layout`高度是`.center`高度
* 绝对定位
```html
<style>
.layout-absolute{
  position: relative;
  .left{
    position: absolute;
    left: 0;
  }
  .right{
    position: absolute;
    right: 0;
  }
  .center{
    position: absolute;
    left: 200px;
    right: 200px;
  }
}
</style>
<section class="layout layout-absolute">
  <div class="side left"></div>
  <div class="center">absolute布局</div>
  <div class="side right"></div>
</section>
```
  * 优点：兼容性好，布局快捷
  * 缺点：元素脱离文档流，`.layout`高度不能撑开
* table
  * 优点
  * 缺点
* flex
  * 优点
  * 缺点
* grid
  * 优点
  * 缺点

### 扩展问题
* 高度不确定时，哪些不适用

* 中间元素高度增加，左右元素能自适应增加高度吗？

* 最优方案

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