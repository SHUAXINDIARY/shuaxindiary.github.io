---
title: docker - 初探上
category:
  - 技术笔记
date: 2022-05-08 17:11:23
tag:
  - docker
index_img: https://img.shuaxindiary.cn/baman.jpeg
banner_img: https://img.shuaxindiary.cn/baman.jpeg
---

> 封面图来源：[SouthCity87](https://twitter.com/SouthCity87)

# 前言

最近搞了自己的服务器，需要部署一些自己的小玩具什么的，不希望重复的折腾两套环境什么的，所以记录一下学习的docker的倒腾历程；初定分为两篇记录初探的笔记：
- docker初探上，基本使用
  - 记录一些基本概念和基本操作，可以run一些社区已有的基于docker的应用来玩
- docker初探下，实战演练
  - 记录一些更实际的操作，封装自己的镜像、生产环境中各个容器之前的交互等等

# 基本概念

### docker是什么

最开始学习的时候，可以把他理解为一个占用物理机资源更小，且更方便管理的虚拟机（*当然两者有这很多区别，但是本质差不多，可以这么理解*）；有三个基本的概念：镜像、容器、仓库。

### 容器是什么

抽象的理解为运行的虚拟机

### 镜像是什么

可以抽象的理解为装系统时的镜像文件，就是用来启动容器时所用到的文件，其中包含容器运行的程序、资源（预装的软件）、配置等

### 仓库

类似github仓库管理项目源代码，docker的仓库用来管理镜像文件；[地址](https://hub.docker.com/)

# 基本操作

### 安装docker

- [参考本文](https://yeasy.gitbook.io/docker_practice/install)

### 镜像管理

这里最基本的操作：拉取镜像、查看本地镜像、删除镜像

```shell

# 拉取镜像 
# docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
# 不指定仓库地址时，默认仓库地址为 docker hub
docker pull ubuntu:18.04

# 查看本地镜像
docker image ls

# 删除镜像
# docker image rm [image id] 
docker image rm xxx

```

除过上述的基本操作，还有个别平时会经常用到的镜像操作，例如：查看镜像所占资源大小、查看镜像时做一些条件筛选、格式化展示信息等

```shell

# 查看镜像所占物理机资源
docker system df

# 查看全部镜像
docker image ls -a

# 查看部分镜像 举例：查看Ubuntu镜像
docker image ls ubuntu

# 只列出所有镜像id
docker image ls -q

```

更多操作

![](https://img.shuaxindiary.cn/1652003566629.png)

### 容器管理

对容器的常用操作：通过镜像创建一个容器、查看容器、终止容器、进入容器、删除容器

```shell

# 运行容器
# docker run [选项] [镜像名称]
docker run nginx 

# 后台运行容器
docker run -d nginx

# 进入运行的容器终端
# docker exec -i [容器id]
docker exec -i b9e4ef2d7fdb

# 查看容器
docker container ls

# 停止运行的容器
# docker container stop [容器id] 
docker container stop b9e4ef2d7fdb

# 重启终止的容器
# docker container restart [容器id]
docker container restart b9e4ef2d7fdb

# 删除容器
# docker container rm [容器id]
docker container rm b9e4ef2d7fdb

```

### 容器和外部通信

外部可以访问容器的网络应用，通过run容器的时候，指定端口映射来实现

```shell

# 随机映射容器内端口和物理机端口
docker run -d -P nginx

# 查看访问记录
docker logs fa

# 映射端口所有地址
docker run -d -p 80:80 nginx

# 映射指定地址端口
docker run -d -p 127.0.0.1::80 nginx:alpine

# 查看容器端口映射
# docker port [容器id]
docker port 85023a0b27c3

```

# 小结

下一篇记录一下一些docker在实际过程中的操作，比如构建自己的镜像、镜像管理、容器之间通信以及管理容器编排工具相关的

