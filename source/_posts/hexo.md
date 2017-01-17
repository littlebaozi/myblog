---
title: hexo
date: 2016-02-01 10:29:44
tags:
- hexo
categories: 
- 工具
---

本来想更新下版本，结果各种报错，干脆重新建一下，记录下过程。
hexo的安装使用文档在[这里](https://hexo.io/zh-cn/docs/)。

## _config.yml
需要配置的地方：
```yml
language: zh-CN #不同主题不一样
url: https://littlebaozi.github.io #website url
theme: icarus #主题
```
<!--more-->

## package.json
* hexo-admin
这个可以进入`localhost:4000/admin`，进行后台管理UI，可以写文章、删除文章、发布等操作。[更多配置](https://github.com/jaredly/hexo-admin)。

* hexo-deployer-git
用来发布到git page。_config.yml中配置如下:
```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: #git page的地址
  branch: master
```
[更多配置](https://github.com/hexojs/hexo-deployer-git)。

* hexo-generator-feed
用来生成RSS。_config.yml中配置如下:
```yml
# RSS
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
```
[更多配置](https://github.com/hexojs/hexo-generator-feed)。

## 主题
主题使用了[icarus](https://github.com/ppoffice/hexo-theme-icarus)
配置如下：
```yml
# Menus,中文要改左边
menu:
  主页: .
  归档: archives
  分类: categories
  标签: tags
# 关于: about
  GitHub: https://github.com/littlebaozi
 
# Customize
customize
    social_links：
        rss: /atom.xml

# Comment
comment:
    duoshuo: abc # 去多说注册下，enter duoshuo shortname here

# Plugins
plugins:
     google_analytics:  # 谷歌统计 enter the tracking ID for your Google Analytics
     baidu_analytics:  # 百度统计 enter Baidu Analytics hash key
```

## 遇到的问题
* 浏览器访问出现CAN NOT GET/：`npm isntall`再安装下依赖
* category页面和tags页面标题英文：修改source/categories/index.md、source/tags/index.md

## 写作
* 写草稿：`hexo new draft <title>`；发布草稿：`hexo publish <title>`
* 文章tags、categories
```
---
tags:
- hexo
categories: 
- 工具
---
```
* 文章readmore，在想要截断的地方写：`<!--more-->`