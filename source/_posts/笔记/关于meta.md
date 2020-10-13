---
title: 关于meta
category:
  - 笔记
tag:
  - 前端
  - 踩坑
abbrlink: 24893
date: 2019-11-08 18:05:39
---
# meta是什么

meta标签提供关于HTML文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。它可用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他 web 服务。

# 作用

提供文档的描述信息

永远位于head内部

永远以键值对形式存在

# 组成

## name

> 描述文档信息,以下列举常见参数

<meta name="参数" content="描述内容">
```
- 1.keywords---关键字
```html
<!-- 用于告诉搜索引擎，你网页的关键字 -->
<meta name="keywords" content="博客，前端">
```
- 2.description---描述
```html
<!-- 用于告诉搜索引擎，你网站的主要内容 -->
<meta name="description" content="学习笔记">
```
- 3.viewport---移动窗口
```html
<!-- 这个属性常用于设计移动端网页 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
- 4.robots---定义爬虫搜索方式
```html
<!-- robots用来告诉爬虫哪些页面需要索引，哪些页面不需要索引 -->
<!-- content的参数有all,none,index,noindex,follow,nofollow。默认是all。 -->
<!-- 1.none : 搜索引擎将忽略此网页，等价于noindex，nofollow。
2.noindex : 搜索引擎不索引此网页。
3.nofollow: 搜索引擎不继续通过此网页的链接索引搜索其它的网页。
4.all : 搜索引擎将索引此网页与继续通过此网页的链接索引，等价于index，follow。
5.index : 搜索引擎索引此网页。
6.follow : 搜索引擎继续通过此网页的链接索引搜索其它的网页。 -->
<meta name="robots" content="none">
```
- 5.author---作者
```html
<meta name="author" content="刷新">
```
- 6.generator---制作软件
```html
<!-- 用于标明网页是什么软件做的 -->
<meta name="generator" content="vscode">
```
- 7.copyright---版权
```html
<meta name="copyright" content="***所有">
```
- 8.revisit-after---定义爬虫重访时间
```html
<!-- 如果页面不是经常更新，为了减轻搜索引擎爬虫对服务器带来的压力，可以设置一个爬虫的重访时间。如果重访时间过短，爬虫将按它们定义的默认时间来访问。 -->
<meta name="revisit-after"  content="7 days">
```
- 9.renderer---双核浏览器渲染方式(360)
```html
<meta name="renderer" content="webkit"> //默认webkit内核
<meta name="renderer" content="ie-comp"> //默认IE兼容模式
<meta name="renderer" content="ie-stand"> //默认IE标准模式
```
## http-equiv
>http请求头作用，但在h5标准中不包括cache-control、Pragma、Expires，即使设置了也不会生效
```html
<meta http-equiv="参数" content="具体的描述">
```
- 1.content-Type---网页字符集
```html
<!-- 用于设定网页字符集，便于浏览器解析与渲染页面 -->
<!-- 旧 -->
<meta http-equiv="content-Type" content="text/html;charset=utf-8">
<!-- h5 -->
<meta charset="utf-8">
```
- 2.X-UA-Compatible---浏览器采取何种版本渲染当前页面
```html
<!-- 默认最新 -->
 <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/> //指定IE和Chrome使用最新版本渲染当前页面
```
- 3.cache-control---请求遵循的缓存机制
```html
<meta http-equiv="cache-control" content="no-cache">
```
- 4.expires---网页到期时间
```html
<!-- 用于设定网页的到期时间，过期后网页必须到服务器上重新传输。 -->
<meta http-equiv="expires" content="Sunday 26 October 2016 01:00 GMT" />
```
- 5.refresh---自动刷新页面
```html
<!-- 网页将在设定的时间内，自动刷新并调向设定的网址。 -->
<meta http-equiv="refresh" content="2；URL=http://www.lxxyx.win/"> //意思是2秒后跳转向我的博客
```
- 6.Set-Cookie---设置cookie
```html
<meta http-equiv="Set-Cookie" content="name, date"> //格式
<meta http-equiv="Set-Cookie" content="User=Lxxyx; path=/; expires=Sunday, 10-Jan-16 10:00:00 GMT"> //具体范例
```
