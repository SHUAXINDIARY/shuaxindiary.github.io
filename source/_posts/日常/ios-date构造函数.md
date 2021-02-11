---
title: ios-date构造函数
category:
  - 日常
date: 2021-02-11 09:33:37
tag:
  - JavaScript
index_img:
banner_img:
---

在apple的js环境中，Date函数的一个坑

> **V8**
> - 以下两种格式都可以
```js
let one=new Date('2020-1-1')
console.log(one) // Wed Jan 01 2020 00:00:00 GMT+0800 (中国标准时间)
let two=new Date('2020/1/1')
console.log(d) // Wed Jan 01 2020 00:00:00 GMT+0800 (中国标准时间)
```

> **Apple**
> - 只能使用 xxxx/xx/xx这样的格式传递
```js
let one=new Date('2020-1-1')
console.log(d) // Invalid value
let two=new Date('2020/1/1')
console.log(d) // Wed Jan 01 2020 00:00:00 GMT+0800 (中国标准时间)
```


