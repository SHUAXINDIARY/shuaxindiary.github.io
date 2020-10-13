---
title: 《学习JavaScript数据结构与算法》笔记---栈
categories:
  - 笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 26833
date: 2019-07-02 11:38:10
---

# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%85%B3%E4%BA%8E%E6%A0%88.html)

##### 概念


<font size=5> 
栈的特点就是后进先出。栈有栈顶和栈底的概念。用数组举例子，数组第一个位置这边就是栈底，最后一个元素的位置就是栈顶，新添加的元素保存在栈顶，在
js中基本数据类就是存储在栈中。
数组模拟就是给数组尾部添加元素，移除元素也是从数组尾部移除。

</font>

![示例图](/img/stack.png)

<!-- more -->
##### 创建一个栈 


``` javascript
 function Stack(){
     /*通过数组来保存栈的元素*/
     let item=[];
 };
```
##### 给栈添加一些方法

1. push 添加一个或几个元素到栈顶
2. pop移除并返回栈顶的元素
3. isEmpty 判断栈是不是空的
4. clear 清空栈内的元素
5. size返回栈内元素个数
6. peek返回栈顶的元素

##### 实现

``` javascript
function Stack() {
    // 通过数组保存栈的元素
    let item = [];
    this.push = function (val) {
        return item.push(val);
    };
    this.pop = function (val) {
        return item.pop(val);
    };
    this.isEmpty = function (val) {
        return item.length == 0;
    };
    this.clear = function () {
        item = [];
    };
    this.size = function () {
        return item.length;
    };
    this.peek = function () {
        return item[item.length - 1];
    };
    this.print = function () {
        for(let i=item.length-1;i>=0;i--){
            console.log(item[i]);
        };
    };
};

```
***ES6版本***
```javascript
class Stack {
    item = [];
    constructor() {};
    push(val) {
        return this.item.push(val);
    };
    print() {
        for (let i = this.item.length - 1; i >= 0; i--) {
            console.log(this.item[i]);
        };
    };
    pop(val) {
        return this.item.pop(val);
    };
    isEmpty(val) {
        return this.item.length == 0;
    };
    clear() {
        this.item = [];
    };
    size() {
        return this.item.length;
    };
    peek() {
        return this.item[this.item.length - 1];
    };
};
```

## demo 做一个10进制和其他进制的转换

``` javascript
/*有两个参数 第一个参数要转换的数字 第二个参数是要转换成的进制*/
function conversion(num, type) {
    while (num / type) {
        stack.push(num % type);
        // 确保每次计算后都是整数
        num = parseInt(num / type);
    };
    stack.print();
    // 处理完成后对栈进行清除
    stack.clear();
};
```
