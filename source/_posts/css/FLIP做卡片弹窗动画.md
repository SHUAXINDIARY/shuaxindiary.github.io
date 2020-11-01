---
title: FLIP做卡片弹窗动画
tag:
  - CSS&HTML
  - JavaScript
abbrlink: 11009
date: 2019-11-08 18:29:49
category:
    - 技术笔记
---

# 惯例BB
最近在做一个卡片弹窗的动画，怎么做都达不到想要的效果，查了查资料，发现通过一种叫FLIP的技术(也不算新技术，一种思路？)，可以比较好的实现这个效果，而且这个过程中，也了解到了一些关于动画性能相关的知识，所以记录一下。
<!-- more -->

[类似效果](https://user-gold-cdn.xitu.io/2019/4/27/16a5dfd7c4d0f639?imageslim)

# 关于FLIP
FLIP是First、Last、Invert和Play四个单词首字母的缩写。
>简单解释下这四个单词在实现思路中对应的意思
- first：指在过度效果发生前，目标对象的位置、大小
- last：指过度效果发生后，目标对象的位置、大小
- invert：目标对象过度前后的位置、大小差
- play：通过invert得到的差值，来作为transform的参数进行动画

# 实现思路
1.动画前后的效果，用两个div写好

2.获取这两个div的大小比例和位置差

3.通过translate将动画后的div变成动画前的大小和移动到对应的位置

4.通过取消3中的translate和重新设置，加上过度就可以实现动画效果啦


# 手撕代码前
代码使用到的API
>transform:translate/scale
- 偏移/缩放

>getBoundingClientRect
- 获取dom元素的位置和大小

# 关于性能
![浏览器渲染过程](/img/flip.png)
上图中的layout和paint是非常影响性能的，比如position和display控制显示隐藏，都会触发这两步，所以在动画过程实现中，位置用transform的translate、显示隐藏用visibility/opacity 来做。因为他们只会触发最后一步。

# 代码演示
```html
...
<style>
body {
    margin: 0;
    position: relative;
}

div {
    text-align: center;
    background-color: rgb(139, 206, 248);
}

.before {
    width: 300px;
    height: 300px;
    line-height: 300px;
    margin: 0 auto;
    transition: all 1s;
}

.after {
    width: 100vw;
    height: 100vh;
    line-height: 100vh;
    position: absolute;
    top: 0;
}
</style>
...
<body>
    <div class="before"></div>
    <div class="after"></div>
</body>
<script>
let before = document.querySelector('#before');
let after = document.querySelector('#after');
// 获取过度前 位置大小
let first = before.getBoundingClientRect();
// 获取过度后 位置大小
let last = after.getBoundingClientRect();
console.log(first);
console.log(last);

// 记录前后大小位置差
let invert = {
    left: first.left - last.left,
    top: first.top - last.top,
    width: first.width / last.width,
    height: first.height / last.height,
};
// 让after通过transform变成before的大小，以及移动到before的位置
after.style.visibility='hidden';
after.style.transformOrigin = 'top left';
after.style.transform = `translate(${invert.left}px,${invert.top}px) scale(${invert.width},${invert.height})`;
// 放大
before.addEventListener('click', function () {
    after.style.transform = '';
    after.style.transition = ' all 1s';
    after.style.visibility='visible';

});
// 缩小
after.addEventListener('click', function () {
    after.style.visibility='hidden';
    after.style.transition = ' all 1s';
    after.style.transform =
        `translate(${invert.left}px,${invert.top}px) scale(${invert.width},${invert.height})`;
});
</script>
...
```

# 参考链接
[浏览器渲染原理](https://segmentfault.com/a/1190000019713766#articleHeader5)
[前端动画之FLIP技术](https://juejin.im/post/5da689b76fb9a04e143dbebc)

