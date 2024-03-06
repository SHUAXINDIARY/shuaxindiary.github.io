---
title: vue-cli3配置
category:
  - 技术笔记
tag:
  - Vue
  - 踩坑
abbrlink: 419
date: 2019-10-14 19:26:57
---

# vue-cli3.0常见配置
<!-- more -->
### 配置打包后资源依赖路径
>创建vue.config.js
```js
module.exports={
    publicPath: process.env.NODE_ENV === 'production'
        ? '/TODO/'
        : '/'
};
```

### 配置请求本地json文件
>1.通过配置webpack的[devserver](https://webpack.js.org/configuration/dev-server/) 
>2.导入expres来响应路由请求，并返回json文件内容
```js
const express = require('express')
const app = express()
var apiRoutes = express.Router()
app.use('/api', apiRoutes)
// 加载本地的json文件
var globalData = require('[你的json文件路径]');
module.exports={
    // webpack devServer的配置
    devServer: {
        // host: 'localhost',
        // port: '8080',
        before(app) {
            app.get('[路由路径]', (req, res, next) => {
                res.json({
                    // 这里是你的json内容
                    errno: 0,
                    data: globalData(上面引入的json文件变量)
                })
            })
        },
    },
}
```
