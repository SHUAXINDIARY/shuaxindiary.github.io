---
title: 《学习JavaScript数据结构与算法》笔记---队列
category:
  - 笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 12013
date: 2019-07-02 23:04:43
---

# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%85%B3%E4%BA%8E%E9%98%9F%E5%88%97.html)

## 概念

<font size=5>
队列是单向操作，而且是先进先出，所以在队尾进行添加元素，队头移除元素。
用数组来理解就是，从数组尾部添加，数组头部删除这样才能模拟先进先出。  
</font>  

![示例图](/img/queue.png)

<!-- more -->
##### 创建一个队列


``` javascript
 class Queue(){
     /*通过数组来保存队列的元素*/
     item=[];
 };
```



##### 给队列添加一些方法

``` javascript
class Queue{
    item=[];
    // 给队列队尾添加元素
    enqueue(val){
        return this.item.push(val);
    };
    // 给队列队头删除元素
    dequeue(val){
        return this.item.shift(val);
    };
    // 返回队列第一个元素
    front(val){
        return this.item[0];
    };
    // 判断队列中是否含有元素
    isEmpty(val){
        return this.item.length;
    };
    // 判断队列的元素个数
    size(){
        return this.item.length;
    };
};
```