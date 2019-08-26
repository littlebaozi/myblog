---
title: 【译】我是如何学习前端框架的
date: 2019-08-26 09:04:24
tags: 框架
category: 翻译
---

> 原文：https://dev.to/imm9o/how-i-learn-any-front-end-framework-29a2

你决定学习XX框架，然后你打开YouTube或者随便一个搜索引擎，搜索到了一些和“XX”框架的相关教程。在30分钟后，你突然大叫“找到了”（Eureka，古希腊词语，据传，阿基米德在洗澡时发现浮力原理，高兴得来不及穿上裤子，跑到街上大喊：“Eureka”），发现这个框架和我之前用的框架很相似。你是对的，你没必要再从头学习这个框架（from scratch：从头做起）。在这篇文章中，我会展示我学习框架的经验和这些框架是如何得相似。

每次你决定学习一个前端框架时，你一定经常看到这些术语：组件、路由、状态管理。
让我们来分解他们。

<!-- more -->

## 组件
* 任何框架的核心是创建组件进行复用。
* 大多数现代框架使用**JSX**或者**HTML**模板引擎。
* 所有的框架都提供**生命周期钩子**，用于提供组件生命时刻的可见性（如创建、渲染、销毁）和这些生命时刻发生时的动作能力。

## 路由
* 现如今大多数现代框架都提供了创建和管理客户端路由的API。

## 状态管理
* 所有的框架都内建了能够让组件无需外部库或工具的情况下，在内部管理自身状态的方法。
* 许多框架内建了可以让组件共享状态的方法（例如，**Angular**有[Service](https://angular.io/guide/architecture-services)，**React**现在有了[Context API](https://reactjs.org/docs/context.html)）。
* 有时候框架自身的解决方案是不够的。特别是当你的状态很庞大时，你可以考虑使用像[redux](https://redux.js.org/)这样的库。

在学习完基础之后，让我们开始动手（ get someone's hands dirty 自己动手）创建项目。

## 创建项目

![framework](https://res.cloudinary.com/practicaldev/image/fetch/s--TmOso3iG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkgvWmTl-lX4G4pA-HoX_cQ.jpeg)