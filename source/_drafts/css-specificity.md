---
title: CSS优先级
tags: CSS
category: 开发
date: 2019-09-25 08:50:05
---


## 基础规则
* 优先级相同时，后面的会覆盖前面的
* html的class没有先后影响

<!-- more -->

<script async src="//jsfiddle.net/littlebaozi/rv9pw4qL/embed/html,css,result/"></script>

## 选择器规则
优先级递减
1. 内联样式（`style="color: white"`）
2. id选择器（#example）
3. 类选择器、
4. 类型选择器（h1, div）和

关系选择符（`+ > ~ ' ' ||`）不影响优先级

## `!important`
如果设置了`!important`，就会覆盖其他所有的样式。
