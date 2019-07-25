---
title: Travis自动部署hexo到git pages
date: 2019-07-25 10:09:27
tags: 折腾
---

[参考文章](https://segmentfault.com/a/1190000009054888)

## 生成token
右上角头像 - setting -  Developer settings - Personal access tokens - Generate new token

## Travis配置
* Environment Variables
变量名 REPO_TOKEN 值 github生成的token