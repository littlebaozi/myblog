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

  * 一行解题
```javascript
const reverseWords = s => s.split('').reverse().join('').split(' ').reverse().join(' ')
```

3. 知识点
  1. `str.split([separator[, limit]])`
    * 参数：separator（必须，字符串或正则）、limit（置顶返回的数组最大长度）。
    * separator是空字符串`""`，则会分割每个字符
  2. `arr.join([separator])`
  3. `str.match(regexp)` regexp有g，执行全局搜索
  4. 正则
    * 元字符`\s`匹配任意空白符
    * 反义`\S`匹配任意不是空白符的字符
    * 标志`g`全局搜索