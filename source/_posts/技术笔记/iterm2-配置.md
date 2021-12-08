---
title: iterm2-配置
category:
  - 技术笔记
date: 2021-12-08 09:16:38
tag:
  - 工具
  - zsh
index_img:
banner_img:
---

# 记录iterm2配置

记录一下自己常用的 **iterm** 终端的配置，效果图如下，主要分为两部分：针对外观和功能扩展

![preview](https://img.shuaxinjs.cn/iterm%E9%85%8D%E7%BD%AE.png)

### 外观

#### 主题配置

- 设置路径：iterm -> preference -> Appperence
- Theme：minimal
- status bar localtion：bottom

#### 配色方案

- 下载地址：https://draculatheme.com/iterm
- 设置：Preferences -> Profiles -> Color -> Color Presets-Import

#### 状态栏展示

- 设置路径：iterm -> preference -> profile -> session
- 勾上status bar enabled
- 点击旁边的 configure status bar
- 根据自己需要选择

### 功能

#### 安装 oh-my-zsh

- 下载安装文件：https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh 
- 建议访问copy内容到本地文件然后 sh运行直接安装

#### 插件

oh-my-zsh 的插件和主题都有单独的目录：
```shell
# 进入
cd $ZSH_CUSTOM

# 当前目录 下会有一个theme和plugin分别存放oh-my-zsh的主题和插件 
ls
# theme plugins

```

下载插件到plugin目录后，编辑 .zshrc 文件，找到plugin，里面填自己下的插件，然后 source 一下 .zshrc 即可

##### 输入建议

- 插件地址：https://github.com/zsh-users/zsh-autosuggestions

##### git扩展

- 插件地址：https://github.com/tj/git-extras
