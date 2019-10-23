---
title: Mac OS上好用到免费软件
date: 2019-10-02 20:09:23
tags: mac
category: 日常
---

# 好用的免费软件

## eZip
 [下载地址](https://ezip.awehunt.com/)

个人开发者业余开发，但功能强大，支持 rar, zip, 7z, tar, gz, bz2, iso, xz, lzma, apk, lz4 等超过 20 种常见压缩格式，且永久免费。

 ## 腾讯柠檬清理
 [下载地址](https://mac.gj.qq.com/)

CleanMyMac X挺贵的，其实也就动画花里胡哨，实际功能也就那么些。柠檬清理的功能有垃圾清理、重复文件清理、大文件清理、相似照片清理、应用卸载、隐私清理。状态栏快捷清理垃圾，还可以查看内存占用情况、cpu温度、风扇转速、开启摄像头隐私保护、麦克风隐私保护。

## pock
[下载地址](https://pock.dev/)

主要功能是将Dock显示在touchbar里，还可以显示日期时间、电量等。Dock就可以隐藏里，节省屏幕空间。

## IINA
[下载地址](https://www.iina.io/)

quicktime支持等视频格式也有限。IINA独立开发者开发等免费视频播放器。支持格式多、功能也强大。

## neat download manager
[下载地址](http://www.neatdownloadmanager.com)

多线程下载工具

## OneNote
[下载地址](http://www.onenote.com/download/)，也可以在app store中下载

微软出品等免费笔记软件，还能多终端同步，非常良心。

## Microsoft To Do
[下载地址](https://todo.microsoft.com)，也可以在app store中下载。

微软出品等又一款免费待办软件，也是多终端同步。

## Microsoft Desktop Remote
[下载地址](https://rink.hockeyapp.net/apps/5e0c144289a51fca2d3bfa39ce7f2b06/)

远程连接软件，远程windows不错哦。国区app store无法下载，可以安装beta版本。


# 升级之后
## 升级catalina之后，terminal提示切换zsh
```bash
The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
```
1. 切换zsh之后，需要把bash的设置复制过来

bash：$HOME/.bash_profile 或者 ~/.bash_profile

zsh：$HOME/.zshrc 或者 ~/.zshrc

强制自动生效：
```bash
source ~/.zshrc
```
2. 继续使用bash，不显示提示
```bash
vim ~/.bash_profile  #编辑该文件，在底部增加以下这行
```

`export BASH_SILENCE_DEPRECATION_WARNING=1`

`:wq`保存推出

```bash
source ~/.bash_profile
```