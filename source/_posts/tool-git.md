---
title: git 操作实践
tags:
  - Git
categories: 开发
thumbnail: 'https://git-scm.com/images/logo@2x.png'
date: 2019-07-26 15:36:39
---


## [修改最近一次提交](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)
提交了一次后，发现漏改了文件，还得提交一次，可以用`--amend`将这次改的内容和上次提交合并成一条提交记录
```bash
git add [forgotten-file]
git commit --amend
```

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

## 回滚某次提交
```bash
git log 可以查看提交记录

git reset --hard HEAD^         回退到上个版本
git reset --hard HEAD~3        回退到前3次提交之前，以此类推，回退到n次提交之前
git reset --hard commit_id     退到/进到 指定commit的sha码
```