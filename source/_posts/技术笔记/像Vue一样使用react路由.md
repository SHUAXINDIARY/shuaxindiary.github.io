---
title: 像Vue一样使用react路由
date: 2020-11-14 09:26:55
category:
  - 技术笔记
tag:
  - React
  - 踩坑
---


场景
=====
习惯了vue里面通过配置文件一样来使用路由，react路由跳转和vue一样都是通过对应的跳转组件进行跳转，没啥区别，但是响应url页面的时候，需要使用很多内置的组件来进行书写，有点繁琐，所以记录一下简单的封装一下常用的RouterView组件，来达到像vue一样简单使用

<!-- more -->

思路
=====
- 将复杂的这部分抽离成一个组件，然后传递需要让路由显示的路由对象就好
- 作为刚入坑react的萌新，需要注意 Link和Switch 要对应在同一个Router下
- 用到的React路由组件：详情见[文档](https://reactrouter.com/web/guides/quick-start)
- - BrowserRouter：浏览器里使用的路由
- - Link：跳转
- - Switch：切换显示的组件
- - Route：控制url对应的组件

[代码](https://codesandbox.io/s/broken-shadow-8hx9o?file=/src/index.js:96-109)
=======
- 使用codesandbox在线ide
- 这里需要注意，每个jsx文件都需要单独引入一下React，不然会报错
```js
// RouterView.jsx
import React from "react";
import { Route, Switch } from "react-router-dom";
const RouterView = ({ routes }) => {
  return (
    <Switch>
      {routes.map((item, i) => {
        return (
          <Route
            key={i}
            path={item.path}
            // 这里传递的为当前路由的子路由
            component={() => item.component(item.routes)}
          />
        );
      })}
    </Switch>
  );
};
export default RouterView;

```

react路由的基本使用
=========
- 接受参数：useParams
```js
// 路由
const routes=[
  {
    path: "/home/:id",
    exact: true,
    label: "首页",
    component: Home
  }
]
// 组件
import React from "react";
import { useParams } from "react-router-dom";
export default function () {
  console.log(useParams());//{id:xxx}
  return <div>Home</div>;
}

```
- 将路由对象信息注入当前组件props：withRouter [使用](https://blog.shuaxinjs.cn/2020/11/11/%E6%8A%80%E6%9C%AF%E7%AC%94%E8%AE%B0/antd-menu%E7%BB%84%E4%BB%B6/)

