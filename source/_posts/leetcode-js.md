---
title: LeetCode JavaScript
date: 2019-08-08 08:57:23
tags:
- 算法
- javascript
category: 开发
---

记录一些LeetCode的算法解题

<!-- more -->
## [557. 反转字符串中的单词 III](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)
1. 审题
  * 题目要求反转句子中单词的顺序，而不是整个反转。
  * 单词间空格只有一个
2. 解题
  * 按空格分割
```javascript
// 以空格分割句子为数组
// 遍历数组，将单词分割成字母的数组，反转数组，还原单词
// 拼接单词
const reverseWords = (s) => {
  // split也可以使用正则，此处可以使用：/\s/g
    return s.split(' ').map((item) => {
        return item.split('').reverse().join('')
    }).join(' ')
}
```
  * 按单词分割
```javascript
const reverseWords = (s) => {
  return s.match(/\S+/g).map((item) => {
        return item.split('').reverse().join('')
    }).join(' ')
}
```

* 一行解体
```javascript
const reverseWords = s => s.split('').reverse().join('').split(' ').reverse().join(' ')
```

3. 知识点
* `split`参数可以是正则
* `match`
* 数组方法：`reverse`、`join`