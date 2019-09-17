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

### display
设置元素的flex布局
* flex 容器表现类似`block`
* inline-flex 元素表现类似`inline`

### flex-direction
主轴的方向
### justify-content
主轴方向元素对齐方式
### align-items
交叉轴方向元素对齐方式
### flex-wrap
元素一行排不下时，是否换行

## 元素属性

### flex
#### 1. flex-grow

#### 2. flex-shrink

#### 3. flex-basis

### align-self