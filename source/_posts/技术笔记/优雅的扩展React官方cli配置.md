---
title: 优雅的扩展React官方cli配置
date: 2020-11-06 20:52:43
category:
  - 技术笔记
tag:
  - React
  - 踩坑
---

FB官方的cli也和vue-cli3.x开始一样，隐藏了webpack配置文件，但是官方没有提供类似vue.config.js这种配置入口，如果想扩展只能eject，所以记录一下来自社区的解决方案，通过[react-app-rewired](https://github.com/timarney/react-app-rewired)和[customize-cra](https://github.com/arackaf/customize-cra)不用eject也可以一定程度扩展cli配置

<!-- more -->

react-app-rewired
==================
通过配置*config-overrides.js*文件，来重写cli原本的配置文件


customize-cra
==================
提供了一些便捷的配置方案，例如less、babel相关等

基本使用
=================

- yarn || npm 安装 react-app-rewired customize-cra
- 根目录创建**config-overrides.js**文件
- 修改package.json启动脚本命令，如下
    ```json
    {
        "scripts": {
            "start": "react-app-rewired start",
            "build": "react-app-rewired build",
            "test": "react-app-rewired test",
            "eject": "react-app-rewired eject"
        }
    }
    ```
- 支持less
    ```js
    <!-- 当前文件：config-overrides.js -->
    <!-- 引入重写方法和添加lessloader的方法 -->
    const { override, addLessLoader } = require('customize-cra')
    module.exports = {
        webpack: override(
            <!-- 可以传递一个配置对象作为参数 -->
            addLessLoader()
        )
    }
    ```

扩展配置
=========
- 查看addLessLoader的源码，可以看到内部返回了一个函数，函数的参数是一个对象，即cli原本webpack配置的对象，然后在其基础上做我们想做的扩展，在返回该对象即可；下图为config对象![](http://img.shuaxinjs.cn/webpack.config.png)
- 实战，添加一个支持解析md文件内容的loader，这里使用raw-loader来读取md文件原始内容，然后在具体的文件内部做其他操作
- - 安装raw-loader
- - 获取原始配置下rules ![](http://img.shuaxinjs.cn/webpack.config2.png)
- - 第一个参数是对解析器（parser）的[配置](https://webpack.js.org/configuration/module/#ruleparser)，图中为关闭require.entrue；第二个是对rules解析的[配置](https://webpack.js.org/configuration/module/#ruleoneof)， 配置当一类文件match某一个loader，处理完毕后就退出
    ```js
    <!-- 当前文件：config-overrides.js -->
    const addMdLoader=()=>config=>{
        <!-- 获取rules数组 -->
        let rules=config.module.rules[1].oneOf
        rules.unshift({
            test: /\.txt$/i,
            use: 'raw-loader'
        })
        return config
    }
    module.exports = {
        webpack: override(
            addMdLoader(),
        )
    }
    ```


总结
===========
这篇笔记所有的尝试来自最初对md文件解析的整个需求，所以总结一下react or vue中怎么获取md文件内容

- 直接引入对应的md文件，然后fetch去取
- 通过配置raw-loader，然后直接获取引入的md文件即可拿到内容
- 通过社区的markdown-loader，然后直接引入即可那得到渲染成HTML后的内容
