---
title: Vue.js高仿饿了么外卖 总结
date: 2019-08-30 15:05:27
tags: vue
category: 学习
---

看完视频后总结一下

<!-- more -->

## vue.js使用
### [vm.$set](https://cn.vuejs.org/v2/api/#vm-set)
1. 使用情景
对象新增属性或者数组新增元素，使其具有响应式，并自动更新视图。
2. 使用方式
`vm.$set( target, propertyName/index, value )`

```javascript
export default {
  data: {
    user: {
      name: '名字',
      gender: '男'
    }
  },
  mounted () {
    this.$set(this.user, 'age', 24)
  }
}
```

### [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)
> Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和MessageChannel，如果执行环境不支持，会采用 setTimeout(fn, 0)代替。


1. 使用情景
数据改变后，dom更新不是立即执行的。操作dom的操作写在`$nextTick`中
2. 使用方式

## 1px实现
当`min-device-pixel-ratio`大于等于1.5时，缩小为原来的0.7；当大于等于2时，缩小为原来的0.5倍。
```css
/* stylus */
@media (-webkit-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)
  .border-1px
    &::after
      -webkit-transform: scaleY(0.7)
      transform: scaleY(0.7)

@media (-webkit-min-device-pixel-ratio: 2),(min-device-pixel-ratio: 2)
  .border-1px
    &::after
      -webkit-transform: scaleY(0.5)
      transform: scaleY(0.5)
```
## 多倍图
[设备像素比devicePixelRatio](https://de veloper.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio)

当`min-device-pixel-ratio`大于等于3时，使用3倍图。
```css
/* stylus */
bg-image($url)
  background-image: url($url+"@2x.png")
  @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3)
    background-image: url($url+"@3x.png")
```
## flex布局
一侧固定宽度，一侧自动宽度
```html
<div class="wrapper">
  <div class="left"></div>
  <div class="right"></div>
</div>
```
```css
.wrapper {
  display: flex
}
.left {
  flex: 1;
}
.right {
  flex: 0 0 20px;
}
```

## (better-scroll)[https://ustbhuangyi.github.io/better-scroll/doc/zh-hans/]
1. 初始化better-scroll

2. 点击左侧菜单，列表滚动到对应位置

3. 滚动列表，左侧菜单高亮对应

```html
<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <div class="menu">
        <div class="item border-1px" :class="{'current': currentIndex == index}" @click="selectMenu(index)" v-for="(item, index) in goods" :key="index">
          <!-- ... -->
        </div>
      </div>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul class="food-category">
        <li v-for="(item, index) in goods" :key="index" class="food-list-hook">
          <!-- ... -->
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import BScroll from 'better-scroll'
export default {
  data () {
    return {
      currentIndex: 0,
      goods: []
    }
  },
  methods: {
    selectMenu(index) {

    }
  }
}
</script>
```

## 数字-星星组件（半星）
1. 教程实现
```html
<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass, index) in itemClasses" :key="index" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script>
const STAR_LENGTH = 5 // 星星最大长度
const CLASS_ON = 'on' // 全亮星
const CLASS_HALF = 'half' // 半星
const CLASS_OFF = 'off' // 全暗星

export default {
  props: {
    size: {
      type: Number, // 24/36/48
      required: true
    },
    score: {
      type: Number,
      required: true,
      default: 5 // 不设置默认值， Expected Number, got Undefined
    }
  },
  computed: {
    // 星星大小
    starType() {
      return 'star-' + this.size;
    },
    // 星星样式数组
    itemClasses () {
      // 小数<.5取整 >.5 算半个星，如3.2算3，2.8算2.5
      let classArr = []
      let newScore = Math.floor(this.score * 2)/2 // 小数小于0.5去小数，大于0.5变0.5
      
      // 计算亮星个数
      for (let i = 0; i < Math.floor(this.score); i++) {
        classArr.push(CLASS_ON)
      }
      let hasDecimal = this.score % 1
      // 计算半星
      if (hasDecimal) {
        classArr.push(CLASS_HALF)
      }
      // 计算暗星个数
      for(let j = 0; j < STAR_LENGTH - Math.ceil(newScore); j++) {
        classArr.push(CLASS_OFF)
      }
      return classArr
    }
  }
}
</script>
<style lang="stylus" scoped>
.star
  font-size 0
  .star-item
    display inline-block
    background-repeat no-repeat
  &.star-24
    .star-item
      width 10px
      height 10px
      margin-right 3px
      background-size: 10px 10px
      &:last-child
        margin-right 0
      &.on
        bg-image('images/star24_on')
      &.half
        bg-image('images/star24_half')
      &.off
        bg-image('images/star24_off')
  &.star-36
    .star-item
      width 15px
      height 15px
      margin-right 6px
      background-size: 15px 15px
      &.on
        bg-image('images/star36_on')
      &.half
        bg-image('images/star36_half')
      &.off
        bg-image('images/star36_off')
  &.star-48
    .star-item
      width 20px
      height 20px
      margin-right 22px
      background-size: 20px 20px
      &.on
        bg-image('images/star48_on')
      &.half
        bg-image('images/star48_half')
      &.off
        bg-image('images/star48_off')
  
</style>
```

2. 我的实现
```html
<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass, index) in itemClasses" :key="index" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script>
const STAR_LENGTH = 5 // 星星最大长度
const CLASS_ON = 'on' // 全亮星
const CLASS_HALF = 'half' // 半星
const CLASS_OFF = 'off' // 全暗星

export default {
  props: {
    size: {
      type: Number, // 24/36/48
      required: true
    },
    score: {
      type: Number,
      required: true,
      default: 5 // 不设置默认值， Expected Number, got Undefined
    }
  },
  computed: {
    // 星星大小
    starType() {
      return 'star-' + this.size;
    },
    // 星星样式数组
    itemClasses () {
      // 小数<.5取整 >.5 算半个星，如3.2算3，2.8算2.5
      let classArr = []
      let decimalNum = this.score % 1
      // 计算亮星个数
      for (let i = 0; i < Math.floor(this.score); i++) {
        classArr.push(CLASS_ON)
      }
      // 计算半星
      if (decimalNum > 0.5) {
        classArr.push(CLASS_HALF)
      }
      // 计算暗星个数
      for(let j = 0; j < STAR_LENGTH - Math.round(this.score); j++) {
        classArr.push(CLASS_OFF)
      }
      return classArr
    }
  }
}
</script>
<style lang="stylus" scoped>
.star
  font-size 0
  .star-item
    display inline-block
    background-repeat no-repeat
  &.star-24
    .star-item
      width 10px
      height 10px
      margin-right 3px
      background-size: 10px 10px
      &:last-child
        margin-right 0
      &.on
        bg-image('images/star24_on')
      &.half
        bg-image('images/star24_half')
      &.off
        bg-image('images/star24_off')
  &.star-36
    .star-item
      width 15px
      height 15px
      margin-right 6px
      background-size: 15px 15px
      &.on
        bg-image('images/star36_on')
      &.half
        bg-image('images/star36_half')
      &.off
        bg-image('images/star36_off')
  &.star-48
    .star-item
      width 20px
      height 20px
      margin-right 22px
      background-size: 20px 20px
      &.on
        bg-image('images/star48_on')
      &.half
        bg-image('images/star48_half')
      &.off
        bg-image('images/star48_off')
  
</style>
```

## 格式化日期

1. 教程实现
```javascript
function dateFormat (date, format) {
  if (/(y+)/.test(format)) {
    // RegExp.$1 匹配到的第一个字符串
    // RegExp.$1.length 匹配到的y长度
    // 年不一定是4位 substring(startIndex, endIndex)
    format = format.replace(RegExp.$1, date.getFullYear().toString().substring(4 - RegExp.$1.length))
  }

  var o = {
    'M+' : date.getMonth()+1,            //月份 
    'd+' : date.getDate(),               //日 
    'h+' : date.getHours(),              //小时 
    'm+' : date.getMinutes(),            //分 
    's+' : date.getSeconds()             //秒
  }
  
  for(k in o) {
    if(new RegExp('(' + k + ')').test(format)) {
      // 补零 日期长度 2 匹配长度1 2，不补零 日期长度 1 匹配长度1（不补零） 2（补零）
      var str = o[k].toString()
      format = format.replace(RegExp.$1, RegExp.$1.length === 1 ? str: paddingZero(str))
    }
  }
  
  return format
}

function paddingZero(str) {
  return '00'.substring(str.length)+str
}

var re = dateFormat(new Date(), 'yyyy-MM-dd hh:mm:ss')
```

2. 我的实现
```javascript
function dateFormat (date, format) {
  if (/(y+)/.test(format)) {
    // RegExp.$1 匹配到的第一个字符串
    // RegExp.$1.length 匹配到的y长度
    // 年不一定是4位 substring(startIndex, endIndex)
    format = format.replace(RegExp.$1, date.getFullYear().toString().substring(4 - RegExp.$1.length))
  }

  var o = {
    'M+' : date.getMonth()+1,            //月份 
    'd+' : date.getDate(),               //日 
    'h+' : date.getHours(),              //小时 
    'm+' : date.getMinutes(),            //分 
    's+' : date.getSeconds()             //秒
  }
  
  for(k in o) {
    if(new RegExp('(' + k + ')').test(format)) {
      // 补零 日期长度 2 匹配长度1 2，不补零 日期长度 1 匹配长度1（不补零） 2（补零）
      format = format.replace(RegExp.$1, '00'.substring(RegExp.$1.length > o[k].toString().length ? 1 : 2)+o[k])
    }
  }
  
  return format
}
  
var re = dateFormat(new Date(), 'yyyy-MM-dd hh:mm:ss')
```