---
title: monorepo - 初探
category:
  - 技术笔记
date: 2022-03-20 09:29:03
tag:
 - vue
 - monorepo
index_img: https://img.shuaxindiary.cn/black.jpeg
banner_img: https://img.shuaxindiary.cn/black.jpeg
---

> [banner图出处](https://weibo.com/u/1908957877?topnav=1&wvr=6&topsug=1)

# 前文

最近再开发一个 [vuepress-next](https://v2.vuepress.vuejs.org/) 的主题 - [stella](https://github.com/SHUAXINDIARY/vuepress-theme-stella)，开发的过程中除过主题本身还需要开发与之相关的一些工具生态（例如快速生成应用该主题的vuepress模板、一些主题开发中用到的公用工具库逻辑等），但是零散在多个仓库维护，带来每次都需要打开多个编辑器、github仓库页，以及每次扩展对应工具的逻辑的时候，还需要单独再发包，再安装才能使用等问题，实在有些繁琐，于是开始了解 [monorepo](https://en.wikipedia.org/wiki/Monorepo) 这种单仓库维护多个包的开发模式，至此记录一下学习笔记。

![stella](https://img.shuaxindiary.cn/1647741458691.png)

# 正文

## 关于monorepo

比起 **monorepo** ，平时更常见的还是 **multirepo** ，即一个项目一个git仓库这样的开发模式；而 **monorepo** 就是将分散在各个仓库的模块全部放在一个git仓库，来进行统一的管理。关于这两种代码管理模式的优缺点有很多种，例如： **monorepo** 模式下，我们可以避免 **multirepo** 中需要去开多个编辑器打开不同的项目、需要去手动关联仓库仓库之间的依赖（link）关系、重复去写同样的项目构建、规范文件等等；但是同样，在 **monorepo** 中，随着代码、包越来越多，仓库体积日渐剧增，每次同步本地远程代码的时间以及发包构建的时间也同样带来了 **multirepo** 不会出现的问题

与其将上述的部分case作为优缺点考量，我觉得更像是使用场景的不恰当带来的一些问题，例如某一个项目不会和其他很多项目之间存在很强的依赖耦合仅仅是单纯的工具包，那单独作为一个仓库管理更合理；针对一些项目需要进行一些公用独立逻辑拆分或者生态工具的开发，他们之间是有这种强依赖和关联关系的，但是同时又希望可以作为独立包去使用，那采用monorepo确实会更方便平时的工作；所以个人观点，某一项技术的出现不是银弹，只是针对一些场景的一个解决方案，不能一发解决所有问题，在日常开发（这里针对个人和开源项目）来说，还是要合理的去分析自己的需要去选择适当的技术（公司里因素过多，不做讨论）

虽然针对上述的部分问题，市面上确实也有一些解决的方案（Google、Meta等都是自研了类似的工具来解决全部项目在一个仓库下带来的一些问题），但是这算作企业内部的解决方案，觉得并不是很适合个人开发视角

## monorepo的落地

切换到monorepo的开发模式，需要关心两件事，即包依赖的管理、发包时包版本的管理。目前主流采用方案都是 [lerna](https://github.com/lerna/lerna) + yarn workspace 的方式来进行（现在pnpm也有自带的方案了，有机会在了解一下），lerna作为包版本和发包的管理，yarn来管理包依赖（通过配置lerna使用yarn作为npmclient）

## 简单落地

以下命令，简单新建一个包，然后发布到npm上

```shell

# 新建一个项目
mkdir mono-demo

# 安装lerna
npm i -g lerna

# 初始化monorepo仓库
lenra init

# 新建一个包
lerna create utils

# 安装第三方依赖到全局
lerna add xxx

# 安装第三方依赖到指定package
lerna add xxx --scope=xxx

# 发布一个包
# 发布之前先登录npm
lerna publish

```

> lerna配置文件

```json
{
  // 设置包目录
  "packages": [
    "packages/*"
  ],
  "version": "0.0.0",
  // 使用什么包管理
  "npmClient": "yarn",
  // 启用workspace
  "useWorkspaces": "true"
}
```

# 总结

算是简单入门一下这个模式的开发和简单尝试一下lerna，暂时也就用了这些简单的，关于主题和相关的一些生态还在开发中，其中遇到一些没尝试过的继续更新...


