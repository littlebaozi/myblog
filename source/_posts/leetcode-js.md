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
  * 空格分割
```javascript
// spilit句子为单词数组
// 遍历单词数组，split单词为字符数组并reverse，join字符
// join单词数组
const reverseWords = (s) => {
    return s.split(' ').map((item) => {
        return item.split('').reverse().join('')
    }).join(' ')
}
```
