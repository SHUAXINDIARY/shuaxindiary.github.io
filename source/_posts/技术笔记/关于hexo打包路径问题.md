---
title: 关于hexo打包路径问题
date: 2020-11-08 14:40:43
categories:
  - 技术笔记
tag:
  - 踩坑
  - Hexo
---

问题描述
========
hexo打包部署到gh-pages后，路径资源404问题

解决
=========
- 在hexo的配置文件（_config.yml）中，找到 URL配置选项
- 在url中填写ghpages默认url（一般为 你的githubanme.github.io）
- root写当前目录名

示例
====
在zxy仓库部署项目，
```yml
url: https://你设置的自定义域名（或者ghpage默认路径）
root: /zxy/
```
