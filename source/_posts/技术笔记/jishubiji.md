---
layout: post
title: 更简单的图片懒加载实现
date: 2024-03-14 11:02:57
category:
  - 技术笔记
tags:
  - JavaScript
  - 前端
---

# 前言

开发过程中，发现了一个更方便来做图片懒加载的方法，简单记录一下

# 正文

之前比较常见的对图片进行懒加载的方法，一般都是手动获取到 img dom，然后通过监听 scroll 事件等，来判断 img dom 是否出现在用户视窗（或目标范围），然后在设置真实 url 来加载图片；拆分这个实现过程，中间做了两件事：

    1. 监听img dom的位置
    2. 设置img的真实url 开始加载图片

对于第一步需要来写大量的判断代码，如下

```js
// 获取需要监听的DOM元素
var targetElement = document.querySelector("#target");

// 判断元素是否在视窗范围内的函数
function isElementInViewport(el) {
  var rect = el.getBoundingClientRect();
  return (
    rect.top >= 0 &&
    rect.left >= 0 &&
    rect.bottom <=
      (window.innerHeight || document.documentElement.clientHeight) &&
    rect.right <= (window.innerWidth || document.documentElement.clientWidth)
  );
}

// 处理滚动事件
function handleScroll() {
  if (isElementInViewport(targetElement)) {
    console.log("目标元素进入视窗");
    // 在元素进入视窗时执行你想要的操作
  } else {
    console.log("目标元素离开视窗");
    // 在元素离开视窗时执行你想要的操作
  }
}

// 添加滚动事件监听器
window.addEventListener("scroll", handleScroll);

// 初始化页面时检查一次元素是否在视窗范围内
handleScroll();
```
其次，另一点就是可能项目里对全局挂载事件可能不只有这一处，当挂载到全局的事件很多且没有手动移除，可能带来性能损耗（一直会对其传入的回调分配内存等）

新的实现方式 ，就是通过 [Intersection Observer API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API#%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E7%A4%BA%E4%BE%8B) ，来实现我们的第一步的操作，除过兼容性可能需要关注，基本可以完美替代上述的方案

他的原理也很简单，通过指定一个我们需要监听的 dom，然后显示传入一个回调函数，当这个 dom 出现在视窗（或指定的某个 dom）内时，会调用传入的回调

简单写个例子，这个例子是当我们点击 show 按钮的时候，id 为 panel 的 div 会展示出来，接着就会触发我们的回调

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      div {
        width: 500px;
        height: 500px;
        background-color: rebeccapurple;
        display: none;
      }
    </style>
  </head>
  <body>
    <div id="panel"></div>
    <button id="show">show</button>
    <script>
      const show = document.querySelector("#show");
      const panel = document.querySelector("#panel");
      const func = () => {
        // 。。。业务逻辑
      };
      const panelObserber = new IntersectionObserver(
        (arg) => {
          console.log("debug callback", arg);
          if (arg[0].isIntersecting) {
            func();
          }
        },
        {
          root: document.querySelector("body"),
          threshold: 1,
        }
      );
      panelObserber.observe(panel);
      show.addEventListener("click", () => {
        panel.style.display = "block";
      });
    </script>
  </body>
</html>
```

封装一个 react Hook

```tsx
import { useState, useRef, useEffect } from "react";

const useLazyLoad = (imageSrc: string, placeholderSrc: string) => {
  const [imageSrcState, setImageSrc] = useState(placeholderSrc);
  const imageRef = useRef(null);

  useEffect(() => {
    // 创建IntersectionObserver实例并配置回调函数
    const observer = new IntersectionObserver((entries) => {
      if (entries[0].isIntersecting) {
        // 当图片进入可视区域时，替换图片源
        setImageSrc(imageSrc);
        // 停止观察
        observer.unobserve(imageRef.current!);
      }
    });

    // 观察ref所指向的元素
    if (imageRef.current) {
      observer.observe(imageRef.current);
    }

    // 清理函数：组件卸载时取消观察
    return () => observer.disconnect();
  }, [imageSrc, placeholderSrc]);

  return [imageSrcState, imageRef];
};

export default useLazyLoad;
```
