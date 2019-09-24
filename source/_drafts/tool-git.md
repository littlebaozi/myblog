---
title: git 操作实践
date: 2019-07-26 15:36:39
tags:
- git
categories: 开发
thumbnail: https://git-scm.com/images/logo@2x.png
---

## 已经push的文件，重新ignore
* 笨方法
1. 把文件移出备份并删除，更新`.gitignore`
2. 提交
3. 重新把文件移进来

* 命令
1. 从暂存区里删除目标文件
```bash
git rm -r --cached target
git commit -m "delete target/"
git push origin master
```
2. 更新`.gitignore`
3. 提交