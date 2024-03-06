---
title: demo-cli
category:
  - 技术笔记
tag:
  - 前端
  - GULP
abbrlink: 13818
date: 2020-04-11 20:08:07
---

# 起因
- 习惯了基于集成了各种工具的cli，来进行开发后，有时候回到最最最原始的三剑客(HTML+CSS+JS)，纯手撸的时候觉得效率过于低下。
- 尤其是写CSS的时候，为了避免因为标签权重问题导致的样式错乱，而不停的重复写选择器，还有没有全局管理样式的功能，导致不停的去查查改改。。。
- 基于上述和还有很多一时没想起的问题，所以有了做一个自己的非SPA应用的脚手架的想法
<!-- more -->
# 关于该脚手架
使用用gulp搭建的自动化脚手架

## 适用场景
- 写静态官网
- 练习TS/ES6

## 功能描述
- 支持区分生产/开发环境
- 支持LESS（可选是否使用）
- 支持ES6（模块化不支持）
- 支持TS（可选是否使用）
- 支持生产环境时HTML+CSS+JS+图片压缩

>2020-4-16更新 1.0.0->1.1.0
- 添加了自己封装的工具函数模块
- 添加对AMD模块化支持 [如何使用](https://requirejs.org/)


# 食用指南

## 下载该脚手架
测试阶段，暂时只能通过github来下载
```shell
git clone git@github.com:SHUAXINDIARY/demo-cli.git
```

## 安装依赖
```shell
# 1.先安装node
# 2.全局安装gulp
npm i gulp -g
# 3.安装依赖
npm i 
```

## 目录说明
```js
+ src---开发目录
   + assets---存放其他资源：resetcss、图标等
   + css---样式文件
   + js----js代码
   + less--less文件(可选：不使用无视即可)
   + ts----ts文件(可选：不使用无视即可)
   - index.html---src根目录存放页面文件
- babelrc---babel配置(可自定义参考官方配置)
+ gulpfile.js---gulp配置目录
   - index.js---入口文件
   - Tasks.js---任务方法文件
   - paths.js---打包入口/出口路径
- tsconfig.json--ts配置文件(可自定义参考)
- config.json----配置项目环境：例如是否使用ts/less、配置本地服务器信息
```
## 开始食用
```shell
# 开发环境
npm run dev
# 部署前打包
npm run build
```


## 食用配置
以下配置根据自行情况选择

>是否使用less
- 进入config.js
- 设置less为true

>是否使用ts
- 同上
- ts编译相关配置，参考[官方文档](https://typescript.bootcss.com/tsconfig-json.html)
- 关于ts编译配置支持情况查看[gulp-typescript](https://www.npmjs.com/package/gulp-typescript)

>配置本地服务器信息
- 进入config.js
- 可根据[gulp-connect](https://www.npmjs.com/package/gulp-connect)配置

# 结尾
- [该项目源码](https://github.com/SHUAXINDIARY/demo-cli)