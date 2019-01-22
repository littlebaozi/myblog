---
title: Vultr Ubuntu 18.04安装配置
date: 2018-05-15 11:42:07
thumbnail: https://www.vultr.com/dist/img/section/bird.svg
tags: 科学上网
categories: 折腾
---

## 服务器安装
勾选enable ipv6和enable private networking
{% asset_img additional.jpg 额外设置 %}

<!-- more -->

## 更新系统
```bash
apt-get update
apt-get upgrade
```

## shadowsockets安装
1. [一键安装脚本python版](https://teddysun.com/486.html)
2. 多用户需要防火墙开启端口
3. 网络加速：[google bbr](https://teddysun.com/489.html)

## ss-manager安装
https://blog.tearth.me/ssmgr_1/
https://blog.tearth.me/ssmgr_2/

## v2ray 安装
https://github.com/v2ray
一键安装脚本
```bash
bash <(curl -L -s https://install.direct/go.sh)
```

## 极路由使用
1. ssh 连接路由器
2. 使用[脚本安装](https://github.com/qiwihui/hiwifi-ss)

## swap
https://www.vultr.com/docs/setup-swap-file-on-linux
https://cyhour.com/662/

```bash
swapoff /swapfile # 卸载swap
rm /swapfile # 删除swap文件
```

## node安装
网站deb.nodesource.com维护了nodejs的各版本安装包的PPA，我们可以从该网站上下载执行导入。[安装方法](https://github.com/nodesource/distributions)

## sqlite3安装
1. `yum update sqlite-devel`更新
2. `yum install sqlite-devel`安装
2. 输入`sqlite3`进入sqlite，输入`.quit`退出

## kms
https://teddysun.com/530.html
* 一键安装 `wget --no-check-certificate https://github.com/teddysun/across/raw/master/kms.sh && chmod +x kms.sh && ./kms.sh`
* 查看端口号监听 `netstat -nxtlp | grep 1688`
* 卸载 `./kms.sh uninstall`

Ubuntu 18 需要安装
```bash
apt install glibc-doc
apt install libc6-dev
```

## nginx
### 一、安装Nginx所需的环境
[Ubuntu 18.04.1安装Nginx]https://www.cnblogs.com/yanyh/p/9801466.html

## aria2
[BT种子/磁力链接下载工具：Aria2一键安装管理脚本](https://www.moerats.com/archives/251/)
### AriaNg
[aria2+ariang+nginx linux 离线下载部署](https://www.jianshu.com/p/8124b5b6ef95)
编辑aria2.conf
1.rpc-secret=你的密码把‘你的密码’改为你的密码即可
2.dir=/home/x/Downloads 将路径改为你的下载目录
3.input-file=/home/x/aria2/aria2.session改为你的aria2.session路经
4.save-session=/home/x/aria2/aria2.session改为你的aria2.session路经

## 应用
[CentOS/Debian安装人人影视客户端，下载资源并自动上传到OneDrive网盘](https://www.moerats.com/archives/813)


## linux命令
* 删除文件/文件夹
```bash
rm -f abc.txt # 删除文件
rm -rf abc/ # 删除文件夹
passwd username ## 回车重置密码
```
* nano编辑器
`nano /etc/hosts`打开编辑文件
ctrl+x退出
* 查看swap
```bash
free -h
```
