---
title: flex布局
category: 开发
tags:
  - css
  - 布局
---

## 基本概念

{% asset_img flex.png flex布局 %}

### 轴线
flex布局是基于轴线的，分为主轴（main axis）、交叉轴（cross axis）。默认情况下（`flex-direction: row`），横向是主轴，纵向是交叉轴；当`flex-direction: column`时则是纵向是主轴，横向是交叉轴。

元素是根据轴线排列对齐的。

### 起止线
起止线表示轴线的起点和终点，包含main start、main end和corss start、 corss end

## 容器属性
**第一个属性值为默认值**
### display
设置元素的flex布局
* flex 容器表现类似`block`
* inline-flex 元素表现类似`inline`

### flex-direction
主轴的方向
* row 横向，起点左边
* column 纵向，起点上边
* row-reverse 横向反向排列，起点右边
* column-reverse 纵向反向排列，起点下边

<script async src="//jsfiddle.net/littlebaozi/r3pod489/embed/html,css,result/"></script>

### flex-wrap
元素一行排不下时，是否换行
* nowrap 不换行
* wrap 换行
* wrap-reverse

<script async src="//jsfiddle.net/littlebaozi/wk21hpq5/embed/html,css,result/"></script>

### flex-flow
`flex-direction`和`flex-wrap`的缩写
*  <flex-direction> || <flex-wrap> row nowrap

### justify-content
主轴方向元素对齐方式
* flex-start
* flex-end
* center
* space-between 两边对齐，中间间距相等
* space-around

### align-items
交叉轴方向元素对齐方式
* 
* flex-start
* flex-end
* center
* 

## align-content
多行元素时，交叉轴上到对齐方式
* 
* flex-start
* flex-end
* center
* space-between
* space-around


## 元素属性
### 

### flex-grow

### flex-shrink

### flex-basis
`flex-grow`、`flex-shrink`、`flex-basis`的缩写
* <flex-grow> || <flex-shrink> || <flex-basis>
### flex

### align-self