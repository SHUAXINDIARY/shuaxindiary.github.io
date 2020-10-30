---
title: 《学习JavaScript数据结构与算法》笔记---(双向)链表
category:
  - 技术笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 3574
date: 2019-09-03 19:42:46
---

# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%85%B3%E4%BA%8E%E5%8F%8C%E5%90%91%E9%93%BE%E8%A1%A8.html)

# 和单向链表区别
1.每个节点有两个指针，一个指向上一个节点，一个指向下一个节点
2.除过链表头head还多了一链表尾tail属性
![示例图](/img/doubleLinked.png)

>***双向链表的优点***
>1.从双向链表中的任意一个结点开始，都可以很方便地访问它的前节点和后节点，单向链表都得从第一个节点开始
>2.既可以从头遍历，又可以从尾部遍历

---------------

>***双向链表的缺点***
>1.每次插入删除节点时，需要处理四个引用
>2.相比较单向链表，存储空间要求更大

<!-- more -->
# 封装一个双向链表

```javascript
    function LinkedList(){
        // 头结点
        this.head=null;
        // 链表长度
        this.length=0;
        // 尾结点
        this.tail=null;
        // 节点类 
        function Node(data){
            // 存储指向上一个节点的指针(应用)
            this.prev=null;
            // 存储节点包含的数据
            this.data=data;
            // 存储指向下一个节点的指针(引用)
            this.next=null;
        };
    };
```

# 基本方法和单向链表的方法一样

****但需要注意的时，涉及到要操作前后两个节点时，不仅要处理指向下一个节点的指针，还要处理指向上一个节点的指针****


# 总结
1.遍历链表：通过查看节点的next是否为空来遍历
2.查找指定位置节点：通过设置index，然后在遍历时和目标位置对比来查找