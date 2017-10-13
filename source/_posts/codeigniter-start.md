---
title: CodeIgniter框架入门
date: 2017-09-28 11:02:15
tags: 
- CodeIgniter
categories:
- 框架
- PHP
---

## 一、项目准备
### 1. 下载CodeIgniter
1. [下载3.X](https://codeigniter.com/download)，解压放在服务器目录。
2. 修改`application/config/config`中的$config['base_url']为项目目录
3. 下载[中文语言文件](https://github.com/bcit-ci/codeigniter3-translations)，将simplified-chinese放在`application/language`。然后打开`application/config/config.php`，修改`$config['language']	= 'simplified-chinese';`

### 2. 下载layui
在项目根目录新建static目录。下载[layui](http://www.layui.com/)，解压layui放到static中

### 3. 去除url中的index.php（只是为了好看点）
1. 首先修改Apache的httpd.conf：
找到`LoadModule rewrite_module modules/mod_rewrite.so`，删除最前面的#取消注释。
搜索`AllowOverride None`，改成`AllowOverride All`，貌似改第一个就行了

2. 项目中添加.htaccess
在项目根目录中新建.htaccess文件，写入下面的内容：
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L]
```

3. 修改`application/config/config`中的$config['index_page']为''

### 4. 使用smarty模板
参考这篇[文章](http://codeigniter.org.cn/forums/thread-23745-1-1.html)。
1. layout的使用
在views目录下新建templates目录，在目录下新建`layout.tpl`、`home.tpl`
```html
<!doctype html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{block name=title}{/block}</title>
    <base href="{base_url()}"/>
    <link rel="stylesheet" href="static/layui/css/layui.css">
    <link rel="stylesheet" href="static/layui/layui.js">
    {block name=head}{/block}
</head>
<body>
    {block name=body}{/block}

    {block name=foot}{/block}
</body>
</html>
```
body中只是文字默认会在外面加一层pre标签。
```html
{extends file='layout.tpl'}
{block name=title}首页{/block}
{block name=body}
    这是home
{/block}
```
