---
title: vue element 慕课教程
date: 2019-08-30 15:05:27
tags: vue
category: 学习
---

## utils
### 格式化日期
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
      // format = format.replace(RegExp.$1, RegExp.$1.length === 1 ? o[k].toString() : '00'.substring(o[k].toString().length))+o[k].toString()
    }
  }
  
  return format
}
  
var re = dateFormat(new Date(), 'yyyy-MM-dd hh:mm:ss')
```