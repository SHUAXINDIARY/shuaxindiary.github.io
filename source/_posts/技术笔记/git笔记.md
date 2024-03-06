---
title: git笔记
category:
  - 技术笔记
tag:
  - Git
  - 踩坑
abbrlink: 26666
date: 2019-10-10 11:57:17
---

# 说明
>下文命令中 [ ]内部为具体要填写的数据，并不包括[ ]

>如: git clone [url],实际为 git clone https://****

# GIT学习

## 关于 GIT
>项目的版本管理工具
>多人同时开发项目协作工具

## 使用
>官网下载并安装git 

<!-- more -->
## 常用命令

***初始化仓库***
```shell
# 进入你的项目文件夹 初始化该项目
git init
```
***全局配置***

****注:刚下载好，第一次使用时做这一步****
```shell
git config --global user.name "[name]"
git config --global user.email "[email]"
```

***克隆项目***
```shell
git clone [项目地址]
```

***项目管理***
>增加文件到暂存区
```shell
# 添加指定文件 到暂存区
git add [文件名] [文件名] ...
# 添加指定目录 到暂存区
git add [目录名]
# 添加所有 到暂存区
git add .
```
>将暂存区的文件提交到仓库区
```shell
# 提交暂存区所有文件
git commit -m '[提交备注]'
# 提交暂存区指定文件
git commit [文件名] [文件名] ... -m '[备注]'
```
>撤销暂存区的文件
```shell
# 恢复暂存区文件到工作区
git checkout [文件名]
# 恢复所有
git checkout .
```

***分支***
>如果在vscode，一下操作可以通过软件的界面完成
```shell
# 查看本地所有分支
git branch
# 查看远程仓库分支
git branch -r
# 同时查看本地和远程所有分支
git branch -a 
# 新建分支，但还留在本分支
git branch [分支名]
# 创建分支并切换到新建分支
git checkout -b [分支名]
# 切换分支
git checkout [分支名]
# 删除分支
git branch -d [分支名]
# 合并本地另一个分支代码到当前分支
git merge [目标分支]
```


***远程仓库***
```shell
# 添加远程仓库
git remote add [重命名] [url]
# 将本地仓库推送到远程仓库 master分支(或其他分支)
git push  origin master
# 提交时创建本地分支和远程对应的分支关联
git push -u origin master
# 之后提交关联后的仓库代码时直接
git push
# 将本地仓库代码提交并强行覆盖远程库的代码(慎重操作)
git push -f

# 同步远程仓库代码到本地
git pull [远程分支] [本地分支]
```

## 使用技巧

### ***master分支***
>项目最稳定最完整，随时可上线代码
### hotfix
>修复线上bug
### feature分支
>开发新功能版本
### ***devlop分支***
>功能最新最全分支
