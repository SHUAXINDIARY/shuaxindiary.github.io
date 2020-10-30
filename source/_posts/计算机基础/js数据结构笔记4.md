---
title: 《学习JavaScript数据结构与算法》笔记---(单向)链表
category:
  - 技术笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 38517
date: 2019-09-03 19:04:43
---

# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%85%B3%E4%BA%8E%E9%93%BE%E8%A1%A8.html)

# 概念

1.类似于数组一样存储多个数据的数据结构
2.链表中每个元素称为节点，每个节点包含元素内容和指向下一个节点的指针
![示例图](/img/linked.jpg)

<!-- more -->
# 和数组对比
>***链表的优点***
>1.内存空间不是必须连续的。可以实现灵活的内存管理
>2.链表不必再创建时就确定大小，并且大小可以无限延伸下去
>3.链表再插入和删除数据时，时间复杂度低，效率高

>***链表的缺点***
>1.访问任何一个位置的元素时，都需要从头开始访问(无法跳过第一或任何一个元素)
>2.无法通过下标直接访问元素

-----------------

>***数组的优点***
>1.占用内存空间少
>2.数组内的数据可以随机访问
>3.数据查找效率较高（内存连续）

>***数组的缺点***
>1.插入和删除效率低，因为要移动操作元素之后的所有元素(无法跳过第一或任何一个元素)
>2.数组大小固定，不能动态拓展(大多数语言是这样js的是动态的)

# 封装一个单向链表

```javascript
    function LinkedList(){
        // 链表头
        this.head=null;
        // 链表长度
        this.length=0;
        // 节点类 
        function Node(data){
            // 存储节点包含的数据
            this.data=data;
            // 存储指向下一个节点的指针(引用)
            this.next=null;
        };
    };
```

# 封装一些基本方法
***只列举插入、添加、删除三个方法，其他方法可以查看源代码***
1.append(data)----给链表尾部追加节点方法，参数:节点数据 不返回结果
    ```javascript
        LinkedList.prototype.append = function (data) {
            // 1.创建新节点
            let node = new Node(data);
            // 2.判断是否是第一个节点  也可以根据heae是否为空判断
            if (this.length === 0) {
                this.head = node;
            } else {
                // 当前第一个节点
                let current = this.head;
                // 通过判断每个节点的next是否为空来找到最后一个元素 
                while (current.next) {
                    current = current.next;
                };
                // 然后给将最后一个元素的next指向我们新添加的元素
                current.next = node;
            }
            // 3.更新长度
            this.length++;
        };
    ```
![第一种情况](/img/insert0.png)
![第二种情况](/img/insert1.png)

***不理解的时候画图就好了***

2.toString()----打印每个节点data内容
3.insert(position,data)----向特定位置插入节点，参数:位置索引,插入数据  成功返回1，失败返回0

```javascript
            LinkedList.prototype.insert = function (position, data) {
                // 1.对position做越界判断  即插入位置是否存在
                if (position >= 0 && position <= this.length) {
                    let node = new Node(data);
                    // 2.插入位置是第一个时
                    if (position === 0) {
                        // 让要插入的节点的指针指向原来的第一个节点
                        node.next = this.head;
                        // 让head指向要插入的节点
                        this.head = node;
                    } else {
                        // 标识每一个节点的位置  previous保存前一个节点 
                        let index = 0,
                            current = this.head,
                            previous;
                            // 当index=position时跳出循环
                        while (index++ < position) {
                            previous = current;
                            current = current.next;
                        };
                        node.next = current;
                        previous.next = node;
                    };
                    this.length++;
                    return true;
                } else {
                    return false;
                }
            };
```
4.get(position)-----获取指定位置节点的数据 参数:位置索引  返回获取到的数据或-1
5.indexof(data)-----在链表中查找指定数据 参数:要查找的数据 返回目标数据节点位置 ，没有返回-1
6.update(position,data)-----修改指定位置的数据 参数:位置索引,要修改的数据  成功返回1，失败返回0
7.removeAt(position)-----删除指定位置的节点 参数:位置索引 成功返回删除节点data，失败返回-1
```javascript
            LinkedList.prototype.removeAt = function (position) {
                // 越界判断
                if (position >= 0 && position <= this.length) {
                    let index = 0,
                        current = this.head,
                        previous;
                    if (position === 0) {
                        this.head = current.next;
                    } else {
                        while (index++ < position) {
                            previous = current;
                            current = current.next;
                        };
                        previous.next = current.next;
                    }
                    // 更新长度
                    this.length--;
                    return current.data;
                } else {
                    return -1;
                }
            };
```
8.remove(data)-----根据data移除该data所在节点 参数:要删除的数据 成功返回删除节点data，失败返回-1
9.size()-----返回链表对的元素个数
10.isEmpty()------判断链表是否为空