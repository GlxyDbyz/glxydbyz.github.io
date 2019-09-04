---
layout:     post
title:      Git常用命令速记
subtitle:   Git常用命令简单例子
date:       2017-07-27
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Git
---

## Git常用命令速记

###### 整理自本人的有道历史云笔记

### 一. 常用分支操作命令

##### 1.查看(全部)分支
> git branch [-a]

##### 2.从当前分支创建新的本地分支 branch-01，并切换到 branch-01
> git checkout -b branch-01 

##### 3.切换到本地的另一个本地已经存在的分支 branch-02
> git checkout branch-02

##### 4.删除本地分支 branch-02
> git branch -d branch-02

##### 5.用当前分支创建远程分支 branch-01
> git push [--set-upstream] origin branch-01

##### 6.拉取远程分支 branch-01 到本地，并且命名为 branch-02，切换到 branch-02
> git checkout -b branch-02 origin/branch-01

##### 7.删除远程分支 branch-01
> git push origin --delete branch-01

##### 8.添加全部修改
> git add .

##### 9.提交到本地仓库
> git commit -m '[add] add files'

##### 10.推送到远程
> git push

---

### 二. 常用错误恢复命令

##### 1. 软还原(代码还在)
>  git reset --soft HEAD~/c7f9bcd5    

##### 2. 硬还原(代码不在)
> git reset --hard HEAD~/c7f9bcd5

##### 3. 本地代码覆盖推送到远程
>  git push --force

##### 4. 提交部分代码到其他分支
###### a. 切换到待提交分支 

> git checkout -b hotfix-20190813  origin/hotfix-20190813 

###### b. 提取需要提交的代码的hashs 

> git cherry-pick  q9f9bfd5 kgf9ecc5 ghfkbcdf

###### c. 推送

> git push

---
