---
title: 【译】我是如何学习前端框架的
date: 2019-08-26 09:04:24
tags: 框架
category: 翻译
---

> 原文：[How I learn any front-end framework](https://dev.to/imm9o/how-i-learn-any-front-end-framework-29a2)

> 作者：[Islam Muhammad](https://github.com/IMM9O)

你决定学习XX框架，然后你打开YouTube或者随便一个搜索引擎，搜索到了一些和“XX”框架的相关教程。在30分钟后，你突然大叫“找到了”【Eureka，古希腊词语，据传，阿基米德在洗澡时发现浮力原理，高兴得来不及穿上裤子，跑到街上大喊：“Eureka”】，发现这个框架和我之前用的框架很相似。你是对的，你没必要再从头学习这个框架【from scratch：从头做起】。在这篇文章中，我会展示我学习框架的经验和这些框架是如何得相似。

每次你决定学习一个前端框架时，你一定经常看到这些术语：组件、路由、状态管理。
让我们来分解他们。

<!-- more -->

## 组件
* 任何框架的核心都是创建组件进行复用。
* 大多数现代框架使用**JSX**或者**HTML**模板引擎。
* 所有的框架都提供**生命周期钩子**，用于提供组件生命时刻的可见性（如创建、渲染、销毁）和这些生命时刻发生时的动作能力。

## 路由
* 现如今大多数现代框架都提供了创建和管理客户端路由的API。

## 状态管理
* 所有的框架都内建了在内部管理自身状态的方法，无需组件使用外部的库或工具。
* 许多框架内建了可以让组件共享状态的方法（例如，**Angular**有[Service](https://angular.io/guide/architecture-services)，**React**现在有了[Context API](https://reactjs.org/docs/context.html)）。
* 有时候框架自身的解决方案是不够的。特别是当你的状态很庞大时，你可以考虑使用像[redux](https://redux.js.org/)这样的库。

在学习完基础之后，让我们开始动手【get someone's hands dirty 自己动手】创建项目。

## 创建项目

![framework](https://res.cloudinary.com/practicaldev/image/fetch/s--TmOso3iG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkgvWmTl-lX4G4pA-HoX_cQ.jpeg)

为了理解你需要了解的事物的各个方面，你需要学习知识，这些知识不只是来自读书或者看视频课程，而是来自现实生活中的真正问题。在这篇文章中，我会带给你一些项目的想法。这些想法涵盖了任何你选择的前端框架许多方面。

**注意：**
* 这个主题中列出的项目的难度是递增的。每个项目都是基于前一个项目。
* 这些项目的顺序是从最简单到最全面。

### 1. 查找和显示（模仿）
第一个常用的应用是使用任意一个网站的公开API来模仿开发。尝试使用下拉列表创建一个简单的搜索栏来保存来自端点API的结果。在展现之前检查返回的数据，例如一张图片是否要显示。

#### 端点API示例
* [Github API](https://developer.github.com/v3/)
* [OMDb API](http://www.omdbapi.com/)
* [Spotify Web API](https://developer.spotify.com/web-api/)
* [wunderground API](https://www.wunderground.com/weather/api/)
* [reddit API](https://www.reddit.com/dev/api/)

#### 你将会学到：
* 使用HTTP客户端向端点API发起请求。
* 使用键盘事件监听器，例如一旦用户敲下回车键就请求端点API获取结果数据。
* 学会如何显示单个数据或者一组数据。
* 使用插值【interpolation：窜改； 添写，插补】数据设置显示样式。
* 结构化【Structural：结构的，构造的】的布局
* 主要-详细：在列表的结果中为每个项目增加一个跳转到项目详情页的链接。
* 学习如何从主要页传递数据到详情页。

### 2. 认证应用
在我之前章节提到过的端点API中，有些需要认证。所以，在这一章节我们尝试增加或构建另一个带有登录、注册页的应用。如果用户登录了，就重定向到游湖主页，还要阻止访客用户进入需要登录的页面。

#### 你将会学到：
* 路由守卫：一些页面仅允许身份验证通过的用户访问。
* 如何发送和保存 **JWT**（JSON web token）以发起需要身份认证的请求。

### 3. 增删改查应用
增删改查（CRUD）应用是最流行的前端应用。在这一章节中，你可以构建一个可以离线使用本地存储或者在线服务（像Firebase）的应用，这个应用甚至可以和后端框架集成【integrate：集成】在一起。

#### 项目示例
* 书签应用
* 待办(To-Do)应用

#### 你将会学到：
* 在表单中验证用户输入并在用户输入错误的数据时显示错误提示。
* 如何发起put、delete、post和get的HTTP请求。
* 将你的应用和任何一个后端框架集成在一起。
* 尝试为你的后端框架增加认证能力。

### 4. 聊天应用
在前面的章节中，所有对后端的请求都是**单向的**【unidirectional】。在后端管理状态也没问题。但是在本章节中，我们尝试使用web sockets构建聊天应用。web sockets是**双向的**【bidirectional】。我们不能等到响应后才去更新视图，所以需要另外的方式管理客户端状态。

#### 你将会学到：
* 学会如何使用状态管理解决方案，比如react的redux、angular 2+的ngrx、vuejs的vuex。以及如何将状态管理整合到你的客户端应用中。
* 使你的应用更加响应性（监听网络状态并通知用户新消息）。