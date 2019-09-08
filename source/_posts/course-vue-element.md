---
title: vue element 慕课教程
date: 2019-08-30 15:05:27
tags: vue
category: 学习
---


## 数字-星星组件（半星）
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
      for (let i; i < Math.floor(this.score); i++) {
        classArr.push(CLASS_ON)
      }
      // 计算半星
      if (decimalNum > 0.5) {
        classArr.push(CLASS_HALF)
      }
      // 计算暗星个数
      for(let j = 0; j < STAR_LENGTH - classArr.length; j++) {
        classArr.push(CLASS_OFF)
      }
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
      // var str = o[k].toString()
      // format = format.replace(RegExp.$1, RegExp.$1.length === 1 ? str: '00'.substring(str.length)+str)
    }
  }
  
  return format
}
  
var re = dateFormat(new Date(), 'yyyy-MM-dd hh:mm:ss')
```