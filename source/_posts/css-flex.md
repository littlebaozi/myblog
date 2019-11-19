---
title: flex布局
tags:
  - css
  - 布局
category: 开发
date: 2019-10-29 22:35:32
---


## 基本概念

{% asset_img flex.png flex布局 %}

<!-- more -->

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
* flex-start 主轴起点对齐
* flex-end 主轴终点对齐
* center 居中
* space-between 两端对齐，中间间距相等
* space-around 每个元素周围分配相同的空间，两边的元素间距是中间元素间距的一半
* space-evenly 每个元素之间的间隔相等，间距都相同
* ...

<script async src="//jsfiddle.net/littlebaozi/z210nsLg/embed/html,css,result/"></script>

### align-items
交叉轴方向元素对齐方式
* stretch 子元素没有设置高度或者auto，交叉轴上填充父元素
* flex-start
* flex-end
* center
* baseline 子元素内第一行元素对齐

## align-content
多行元素时，交叉轴上到对齐方式
* stretch
* flex-start
* flex-end
* center
* space-between
* space-around


## 元素属性
### order
设置元素的顺序，值越小越靠前
* <number>

### flex-grow
有剩余空间时，放大比例。正整数。默认有剩余空间不放大
* 0
* <number>

### flex-shrink
空间不够时，缩小比例。正整数。当空间不够时，默认缩小；0表示不缩小
* 1
* <number>

### flex-basis
表示原始大小，默认元素原来大小
* auto
* <length>

### flex
`flex-grow`、`flex-shrink`、`flex-basis`的缩写
* 0 1 auto
* <flex-grow> || <flex-shrink> || <flex-basis>
* auto (1 1 auto) 
* none (0 0 auto)
* <number>(number number 0)

### align-self
设置自身在交叉轴上的对齐方式，比`align-items`少了`baseline`。
* auto 继承容器
* stretch
* flex-start
* flex-end
* center

## 应用实例
### 左侧固定，右侧自适应
<script async src="//jsfiddle.net/littlebaozi/qoty3gs2/embed/html,css,result/"></script>

### 水平垂直居中
<script async src="//jsfiddle.net/littlebaozi/whadrc6t/embed/html,css,result/"></script>
