---
title: React Native 安装配置
date: 2016-03-01 11:00:23
thumbnail: https://www.smartworld.it/wp-content/uploads/2016/04/react-native.png
tags:
- react native
categories: 
- 框架
---

## 一、环境
　　搞环境是很烦的，特别是windows
### 1. 安装JAV
　　从[Java官网](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html)下载JDK，安装好后，设置下环境变量。
```
JAVA_HOME 
    D:\Dev\Java\jdk1.7.0_79
Path 
    %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```
###　2. 安装JDK
　　安装android studio，刚写这篇的时候在[这里](http://www.androiddevtools.cn/)下载的，现在可以不翻墙[下载](https://developer.android.google.cn/studio/index.html)啦。然后设置下环境变量。
```
ANDROID_HOME 
    D:\Dev\Android\sdk
Path 
    %ANDROID_HOME%/tools;%ANDROID_HOME%/platform-tools;
```

<!--more-->

### 3. 安装SDK
1. 打开Android SDK Manager。
2. 选中以下项目：
* Android SDK Build-tools version 23.0.1
* Android 6.0 (API 23)
* Android Support Repository
{% asset_img sdk.png SDK安装 %}

### 4.安装node
　　下载之后，下一步下一步安装就行了。
### 5.安装git
{% asset_img bashsetup_1.png 步骤1 %}
{% asset_img bashsetup_2.png 步骤2 %}
{% asset_img bashsetup_2.png 步骤3 %}

### 6.安装react-native-cli
　　国内npm访问慢，用taobao的npm镜像挺好的。打开git bash设置下，以后就用cnpm啦。
```
npm install -g cnpm --registry=https://registry.npm.taobao.org

cnpm install -g react-native-cli

```

## 二、项目初始化
　　新建一个目录reactNativeProject。
```
cd reactNativeProject
react-native init reactNativeProject

```

## 三、运行packge
```
react-native start
```
　　等一段时间，然手用浏览器访问http://localhost:8081/index.android.bundle?platform=android，如果可以访问表示服务器端已经可以了。

## 四、模拟器
　　手机调试，数据线连接电脑，打开调试模式。也可以装模拟器，推荐Genymotion，选择BASIC是免费的。

## 五、运行项目
　　保持packge开启，新建命令窗口，输入命令，然后等待。BUILD SUCCESSFUL就表示成功了。
　　运行命令
```
react-native run-android
```
　　第一次肯定报错。
{% asset_img reactnative_firstload.png 步骤1 %}
　　摇一摇手机，或者按Menu键，点击Dev Settings后，点击Debug server host & port for device，设置IP和端口（电脑的ip，并且在同一局域网中）：192.168.1.xx:8081，再按back键返回，再按Menu键，在调试菜单中选择Reload JS，就应该可以看到运行的结果了。
{% asset_img reactnative_setdev.png 步骤2 %}
{% asset_img reactnative_setdev_1.png 步骤3 %}
{% asset_img reactnative_setport.png 步骤4 %}

## 六、编辑器
　　推荐webstorm。配置下JSX语法识别。
{% asset_img webstorm_jsx.png webstorm设置 %}

## 七、react-native命令

```
react-native -v  //查看cli版本，项目目录可以查看react-native版本
npm update -g react-native-cli  //更新cli
npm install --save react-native@0.18  //更新react-native，还可以用作降级
react-native upgrade  //降级后也要更新一下

```
------

以上的react-native upgrade会进行检查项目的文件，然后进行如下几个操作:
• 如果是新添加的文件，会进行直接创建
• 如果更新的文件和当前项目的文件是一样的，就会直接忽略跳过
• 如果更新的文件和当前项目的文件不同，有冲突的情况，会让我们进行选择是保留原来的文件还是用更新的文件覆盖，这个要看实际情况了。
