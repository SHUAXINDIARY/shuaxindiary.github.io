---
layout: post
title: 区分工作和私人github账号
date: 2024-03-06 09:42:15
category:
  - 技术笔记
tags:
    - Git
    - 随笔
index_img: https://img.shuaxinjs.cn/20240229-094839.jpeg
banner_img: https://img.shuaxinjs.cn/20240229-094839.jpeg

---

# 前言

有时候会用公司电脑写一些自己的代码然后推到github，但是想commit记录以及相关个人信息和自己电脑的保持一致，不带有任何公司信息，于是简单研究了下怎么搞，这里简单记录一下

# 关于github信息

github每次的commit记录只会记录用户昵称和邮箱，就是每次配置环境的时候设置的 username 和 email ；而推送时的访问权限有登录来做身份认证来，vscode里会在第一次push的时候进行登录验证，所以这里不需要关心，我们要做的只是对不同的仓库切换不同用户信息配置即可

# 实操

### 新建两份信息文件

```shell
# 进入根目录
cd ~

# 新建两份配置文件

# 用来保存公司github信息
touch .gitconfig-work

# 用来保存自己github信息
touch .gitconfig-self

```

上述2个文件结构如下，内容按自己的写即可

```ini
[user]
    name = SHUAXIN
    email = shuaxinjs@qq.com
```

### 仓库内切换

每一个被git初始化过的仓库内部有一个 .git 目录，里面会记录一切git相关的信息，其中有一个 config文件，里面记录了对应的分支、仓库、用户等信息，以本站点的仓库举例，内容大概如下

```ini
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = https://github.com/SHUAXINDIARY/shuaxindiary.github.io.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master

```

我们要做的就是在其中配置我们想要使用的git用户信息即可，给这个仓库设置使用 .gitconfig-self ，操作如下

```ini

[include]
    path = ~/.gitconfig-self

```
