---
title: Vultr Ubuntu 18.04安装配置
date: 2018-05-15 11:42:07
thumbnail: https://www.vultr.com/dist/img/section/bird.svg
tags:
---

## 服务器安装
勾选enable ipv6和enable private networking
{% asset_img additional.jpg 额外设置 %}

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

## nginx安装
[安装教程]()

## ghost安装
[安装教程](https://www.vultr.com/docs/install-and-configure-a-ghost-v1-blog-on-ubuntu-16-04)
https://docs.ghost.org/docs/install

```
sudo mkdir -p /var/www/blog_ghost
sudo chown <johndoe>:<johndoe> /var/www/blog_ghost
cd /var/www/blog_ghost
ghost install
```

* 出现`EACCES: permission denied, scandir '/home/baozi/.config/yarn/link'`错误，运行命令
`sudo chmod 775 /home/baozi/.config/`
* Error: ER_NOT_SUPPORTED_AUTH_MODE
```
# This connects to mysql
mysql -u root
# In the mysql prompt, run:
use mysql;
update user set authentication_string=password(''), plugin='mysql_native_password' where user='root';
flush privileges;
```

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
