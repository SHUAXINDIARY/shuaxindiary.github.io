---
title: 关于js深拷贝
category:
  - 笔记
tag:
  - JavaScript
  - 前端
abbrlink: 26405
date: 2019-07-07 12:11:14
---

# 开头
面试复习，复习到了深拷贝，记录一下
# 正文
## 基本数据类型&复杂数据类型(引用数据类型)
>概念
- 在js种，有2中数据类型：一类是基本数据类型，一类是复杂数据类型；
- 基本数据类型有:null,undefined,number,string,boolean,symbol(ES6)
- 复杂数据类型:Object/arrary等
>区别
- 两者的区别就是在内存中的存储位置不同
- 前者在栈中存储数据
- 后者在堆中存储数据，而栈中存储的是数据在堆中存储的位置(引用)
## 深拷贝
作用与复制复杂数据类型时，因为栈中只保存了数据的地址，所以如果只是简单赋值一样的复制，就会出现如下情况(浅拷贝)
```js
let one={
    one:"this is one"
}
let two=one
console.log(two)  // {one:"this is one"}
one.one=1
console.log(two)  // {one:1}
```
## 深拷贝实现
>思路
- 判断要复制的目标的数据类型
- 1.简单数据类型，直接复制
- 2.复杂数据类型(对象/数组)，遍历内部每一项
- 3.在遍历复杂数据类型内部属性时，会出现情况上面2中情况，再次调用该函数就好(递归)
```js
let deepCopy = (copyTarget) => {
    let res = null
    // 返回和复制对象一致的类型
    if (copyTarget instanceof Array) {
        res = []
    } else {
        res = {}
    }
    for (let key in copyTarget) {
        if (typeof copyTarget[key] === 'object' && copyTarget[key] !== null) {
            // 1.复杂数据类型
            console.warn('复杂数据类型')
            res[key] = deepCopy(copyTarget[key])
        } else {
            console.warn('基本数据类型');
            // 2.基本数据类型/和函数
            res[key] = copyTarget[key]
        }
    }
    return res
}
```