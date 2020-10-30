---
title: 《学习JavaScript数据结构与算法》笔记---集合
category:
  - 技术笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 47845
date: 2019-09-04 18:46:18
---

# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%85%B3%E4%BA%8E%E9%9B%86%E5%90%88%E7%BB%93%E6%9E%84.html)

# 概念

1. 集合是由一组***无序***且***唯一***的项组成的。
2. 可以理解成一种特殊的数组
    1.特殊在里面的元素没有顺序，不能重复
    2.没有顺序意味着不能通过下标访问，不能重复意味着相同对象在集合中只存一份
3. 和数学中的集合一样，也有并集、交集、差集、子集的概念。

<!-- more -->
# ES6的set结构就是一个集合

# 集合常见的实现方式是哈希表

# 封装一个集合类

```javascript
    function SetCustomer(){
        // 通过对象来存储我们的元素 也可以用数组 对象方便操作
        // 属性
        this.items = {};
    };
```

# 动手封装集合的常见操作

>***基本方法***
>1.add(value)向集合添加一个新的项
```javascript
    SetCustomer.prototype.add = function (value) {
        this.items[value] = value;
    };
```
>2.remove(vlaue)移除集合的一项
```javascript
    SetCustomer.prototype.remove = function (value) {
        if (this.has(value)) {
            delete this.items[value];
            return true;
        } else {
            return false;
        }
    };
```
>3.has(value)判断集合有无指定的一项?返回true:返回false
```javascript
    SetCustomer.prototype.has = function (value) {
        if (this.items.hasOwnProperty(value)) {
            return true;
        } else {
            return false;
        }
    };
```
>4.clear()移除集合所有项
```javascript
            SetCustomer.prototype.clear = function () {
                this.items = {};
            };
```
>5.size()返回集合包含项的个数
```javascript
    SetCustomer.prototype.size = function () {
        return Object.keys(this.items).length;
    };
```
>6.values()返回一个包含集合所有值的数组
```javascript
    SetCustomer.prototype.values = function () {
        // 因为存储集合时键值保持一致的 所以取键也行
        return Object.keys(this.items);
    };
```

>***封装两个集合的求集操作***
>1.并集：对于给定的2个集合，返回一个包含两个集合中 所有的 元素的新集合
```javascript
    SetCustomer.prototype.union = function (otherSet) {
        // 1.创建新集合
        let unionSet = new SetCustomer();
        // 2.将A集合元素添加到新集合
        for (let i = 0; i < this.size(); i++) {
            unionSet.add(this.values()[i]);
        };
        // 3.将B集合添加到新集合
        for (let i = 0; i < otherSet.size(); i++) {
            unionSet.add(otherSet.values()[i]);
        };
        // 4.返回新集合
        return unionSet;
    };
```
>2.交集：对于给定的2个集合，返回一个包含两个集合中 共有的 元素的新集合
```javascript
 SetCustomer.prototype.intersection = function (otherSet) {
    let insSet = new SetCustomer();
    for (let i = 0; i < this.size(); i++) {
        if (otherSet.has(this.values()[i])) {
            insSet.add(this.values()[i]);
        }
    };
    return insSet;
};
```
>3.差集：对于给定的2个集合，返回一个包含所有存在A但不存在B集合的所有元素的新集合
```javascript
SetCustomer.prototype.difference = function (otherSet) {
    let diffSet = new SetCustomer();
    for (let i = 0; i < this.size(); i++) {
        if (!otherSet.has(this.values()[i])) {
            diffSet.add(this.values()[i]);
        }
    };
    return diffSet;
};
```
>4.子集：验证一个集合是否是另一个集合的子集
```javascript
SetCustomer.prototype.subset = function (otherSet) {
                if (this.size() > otherSet.size) {
                    return false;
                } else {
                    for (let i = 0; i < this.size(); i++) {
                        if (!otherSet.has(this.values()[i])) {
                            return false;
                        }
                    };
                    return true;
                }
};
```