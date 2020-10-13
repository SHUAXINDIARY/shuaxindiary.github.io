---
title: 《学习JavaScript数据结构与算法》笔记---队列之优先队列
category:
  - 笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 33854
date: 2019-09-02 14:51:44
---
# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E4%BC%98%E5%85%88%E7%BA%A7%E9%98%9F%E5%88%97.html)

## 概念
> 1.优先队列和队列的主要区别就是，队列中每个元素不仅含有自身的元素，还有一个代表该元素优先级的标识。  
> 2.在插入新的元素到队列时，根据该元素的优先级来决定它插入的正确位置。
> 3.优先队列具有最高级先出队列的行为特征


<!-- more -->
### 封装优先级队列

```javascript
     function PriorityQueue() {
         // 封装属性 用数组来存储元素
        this.items = [];
        // 创建一个包含元素内容和优先级的类
        function QueueElement(element, priority) {
            this.element = element;
            this.priority = priority;
        };
        // 实现插入方法
        PriorityQueue.prototype.enqueue = function (element, priority) {
            // 创建QueueElement
            let queueElement = new QueueElement(element, priority);
            // 判断队列是否为空?直接插入:和已有元素对比优先级后插入
            if (this.items.length===0) {
                this.items.push(queueElement);
            } else {
                let added=false;
                for(let i in this.items){
                    if(queueElement.priority<this.items[i].priority){
                        this.items.splice(i,0,queueElement);
                        added=true;
                        break;
                    }
                };
                if (!added) {
                    this.items.push(queueElement);
                };
            }
        }
     }
```

### 测试代码

```javascript
    let pq = new PriorityQueue();
        pq.enqueue('a',1);
        pq.enqueue('b',20);
        pq.enqueue('taotao',19);
        console.log(pq.items);
        /* 控制台打印内容
        (3) [QueueElement, QueueElement, QueueElement]
            0: QueueElement {element: "a", priority: 1}
            1: QueueElement {element: "taotao", priority: 19}
            2: QueueElement {element: "b", priority: 20}
            length: 3
            __proto__: Array(0)
            */
```
