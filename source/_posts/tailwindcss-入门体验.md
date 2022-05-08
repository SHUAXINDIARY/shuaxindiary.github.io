---
title: tailwindcss - 入门体验
category:
  - 技术笔记
date: 2022-03-04 22:11:46
tag:
  - postcss
  - css
  - tailwindcss
index_img: https://img.shuaxindiary.cn/IMG_0371.JPG
banner_img: https://img.shuaxindiary.cn/IMG_0371.JPG
---

> 图片来源 - [黑板Black](https://weibo.com/u/1908957877?from=myfollow_all)


# 前言

最近做 [**github-resume**](https://toy.shuaxinjs.cn/loginGithub) 的时候，体验了一段时间的 **tailwind css** , 记录一些心得。

# 简介

![tailwind-css](https://img.shuaxindiary.cn/1646448840236.png)

- **tailwindcss** 是一个通过组合多个有语义的 *class* 来构建ui的css库。

- 他默认会提供一套设计原则（色系、布局大小、对齐等），然后再这个原则基础上，将每一个css封装成一个一个的有语义的class（所谓的原子粒度），然后通过在组合这些class来构建ui。

```html
<!-- 普通css -->
.title {
  color: #f8fafc;
}
<p class="title">举个例子</p>

<!-- tailwind css -->
<p class="text-Slate-50">举个例子</p>

```



# 安装

```shell

# 进入项目目录
cd projext-xxx

# 安装所需依赖 
# 注1：tailwindcss是基于postcss开发的 
# 注2：autoprefixer是postcss的一个插件 用来解析css和添加浏览器前缀
npm install -D tailwindcss postcss autoprefixer

# 初始化tailwindcss 会自动创建一个tailwindcss配置文件
npx tailwindcss init -p 

```

- 更多可以[参考官网](https://tailwindcss.com/docs/installation/framework-guides)，基本覆盖了各个技术栈开发工具的指南

# 兼容性

- 从官网的描述中得知，完全按最新浏览器css标准实现，可以在 [caniuse](https://caniuse.com/)，查询浏览器是否支持某个特性
- 同时，因为是基于postcss来实现，所以可以通过各个插件来实现一些现在浏览器还不支持的特性，以及一些预处理器可以做到的，tailwind同样可以做到（postcss的能力）

# 特点

- 类似css in js 和行内样式一样的，不用单独维护样式文件
- 但是在支持上述的优点的同时，提供响应式和约束设计，并且支持状态书写（悬浮、聚焦等）、黑暗模式等
- 因为基于postcss实现，所以预处理器相关的特性，也基本都可以通过postcss插件来实现

# 使用

安装完成后，在主css中引入下列代码

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

建议搭配以下插件（vscode），食用更愉快：
- [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)：提供lint、语法高亮、命令补全等
- [Tailwind Docs](https://marketplace.visualstudio.com/items?itemName=austenc.tailwind-docs)：在vs指令中，搜索对应class的使用，然后会自动跳转到文档对应位置查看

![Tailwind Docs](https://img.shuaxindiary.cn/1646465880287.png)

### 基本使用

- 基本就是将css的属性换成了一个单词，然后写在class里，文档类似词典直接搜对应属性即可搜到

### 伪类、伪元素

以hover举例，其他差不多，详见官网

```html

<!-- hvoer -->
<p class="hover:text-red">hover</p>

<!-- 根据父元素状态 -->
<div class="group">
  <p class="group-hover:text-red">子</p>
</div>

<!-- 根据兄弟元素装填 -->
<div>
    <p className="peer">兄弟元素 - 1</p>
    <p className="peer-hover:text-red-800">兄弟元素 - 2</p>
</div>

```

```jsx

// 第一个 最后一个
{data.map((item, i) => {
    return <p key={i} className="first:text-blue-400 last:text-yellow-300">{item}</p>;
})}

```

### 公用样式

公用样式的抽离，有以下几种形式：

- 抽离为js的变量（tsx和vue）来使用
- 使用 **@apply** 指令，抽离 tailwindcss的多个class到一个自定义class里
- 使用 **@layer** 指令，自定义多个dom的css在一个class里，理解为新建一个ui组件（很多基于tailwindcss的组件库就是这样做的）

### 常见配置

```js
module.exports = {
  // 定义
  mode: "jit",
  // 定义tailwindcss在哪些文件生效
  content: [],
  // 定义自己的设计原则（颜色的色系、主题、间距等，tailwindcss自带的都可以修改）
  theme: {
    extend: {},
  },
  // 注册tailwindcss插件  社区基于齐开发的组件什么的都是这样加载的
  plugins: [],
  // 使用自定义配置文件替代默认配置文件
  presets: []
  // 给tailwindcss的所有class添加前缀
  prefix: '',
  // 是否可以使用功能 !important 也可以设置为一个id选择器，将只允许在该dom下才能使用该修饰器
  important: true
  // ... 常用到的就这些，其他详见官网
};

```

# 基本原理

- tailwindcss其实就是postcss的一个插件，然后结合一些其他postcss的插件，来用js解析我们写的 **class（tailwind定义的工具class）**，然后去动态的生成对应的css；而框架本身的设计原则，其实就是默认定义好色系值、间距值等常量，在来决定生成css的时候具体像素以及颜色值该是什么
- 上述只是核心的工作流程，其他还有很多细节，例如本身的插件机制、以及怎么去做css剪裁（css的treeshaking）等。。（计划学习一下postcss在详细看看）

# 总结

- tailwindcss作者思路还是给我眼前一亮的，一个css库除过测试基本没多少css，感觉把postcss玩出花了（nb）


- 技术社区对很多场景的解决方案一直是层出不穷的，个人觉得这些方案，没有绝对的好坏之分，只有是否适合自己当下的需求，再好的方案在不会使用或者使用不当的人，也都会成为”不好的“方案，根据自己需要选择即可


- 另外，其实作为一个技术人，很多方案自己没有想到可以这样做，但是别人想到了并且开源提供给大家使用，我觉得即使不用，去学习一下别人的思路也是可以的，就当技术交流，而不是上来口吐芬芳（吐槽一些技术圈子的陋习）
