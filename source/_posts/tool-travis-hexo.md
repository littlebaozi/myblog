---
title: Travis自动部署hexo到git pages
date: 2019-07-25 10:09:27
tags:
- hexo
categories: 折腾
---

[参考文章](https://segmentfault.com/a/1190000009054888)

## 生成token
右上角头像 - setting -  Developer settings - Personal access tokens - Generate new token

## Travis配置
* Environment Variables
变量名 REPO_TOKEN 值 github生成的token

## .travis.yml
```yml
# 使用语言
language: node_js
# node版本
node_js: stable
# 设置只监听哪个分支
branches:
  only:
  - master
# 缓存，可以节省集成的时间，这里我用了yarn，如果不用可以删除
cache:
  apt: true
  yarn: true
  directories:
    - node_modules
# tarvis生命周期执行顺序详见官网文档
before_install:
- git config --global user.name ""
- git config --global user.email ""
# 由于使用了yarn，所以需要下载，如不用yarn这两行可以删除
- curl -o- -L https://yarnpkg.com/install.sh | bash
- export PATH=$HOME/.yarn/bin:$PATH
- npm install -g hexo-cli
install:
# 不用yarn的话这里改成 npm i 即可
- yarn
script:
- hexo clean
- hexo generate
after_success:
- cd ./public
- git init
- git add --all .
- git commit -m "Travis CI Auto Builder"
# 这里的 REPO_TOKEN 即之前在 travis 项目的环境变量里添加的，name换成自己的
- git push --quiet --force https://$REPO_TOKEN@github.com/name/name.github.io
  master
```