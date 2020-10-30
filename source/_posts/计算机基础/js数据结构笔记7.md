---
title: 《学习JavaScript数据结构与算法》笔记---哈希表
category:
  - 技术笔记
tag:
  - JavaScript
  - 数据结构
abbrlink: 56640
date: 2019-09-10 21:20:45
---
# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E5%93%88%E5%B8%8C%E8%A1%A8%E6%A1%88%E4%BE%8B.html)


# 理论

***特点***
>1.基于数组创建的一种数据结构
>2.存储元素时，对每一个元素通过哈希函数，进行哈希化后存储
>3.存储后的这个数组就叫做哈希表(HashTable，也有叫散列表的)

***和数组对比***
>**优点**
>>比数组更快的查找速度(因为删除，修改基于查找所以效率也提高)

>**缺点**
>>key值不可以重复
>>哈希表没有顺序，不能以一定的顺序遍历

<!-- more -->
***哈希函数***
>1.当在一百万数据的数组中，根据内容查找一个元素和根据那个元素的下标来查找时，时间差距很大
![对比图](/img/hash.jpg)
>2.哈希函数做的事就是把一个元素的key值转换成一个数字，然后在数组中以这个数字为下表存入数据
>3.查找时，也是根据存入时哈希化(使用哈希函数对key进行转换的过程)的key值条件,先对其哈希化然后通过哈希化后的数字去数组中查找
>4.一个好的哈希函数应该具有2个特点:
- 1.快速计算(减少乘法，乘法比加法在计算机中更耗费性能)
- 2.哈希后的数组下标要尽可能平均

***冲突***
>当哈希化key值后出现的数字发生重复时这种现象叫做冲突
>冲突的解决方案
>>1.链地址法(用的更多，下文代码实现也使用这种方式)
- 存储元素时，用数组或链表进行存储

>>2.开放地址法(寻找还是空的位置来存储)
- 线性探测：以一定的规则来寻找下一个空位置来存储，这个规则得出的数一般是一个质数，称做步长.

# 代码实现

***封装哈希表***
>封装方法：
>>哈希函数---hash(key)
- 传入要哈希的key，然后将该key值每个字母转换成ASCII码做和在进行一定的处理，以下函数规则参考自《学习JavaScript数据结构与算法》

>>存储元素---put(obj)
- 传入要存储的元素(下文是存储一个对象)，存储时先通过哈希函数将某个属性(下文是name)哈希化得到要存储的位置，然后在存储时进行判断：1.该位置为空，直接插入；2.不为空，对比查看name值是否相同，不同插入在最后，若相同，覆盖；

>>获取元素---get(str)
- 传入要查找的的内容(存储时哈希化的属性name)，查找时先通过哈希函数将查找值哈希化得到目标位置，然后直接查找，有返回整个对象；无，返回null

```javascript
 function HashTable() {
            //  存储元素的数组
            this.items = [];
            // 哈希函数
            HashTable.prototype.hash = function (key) {
                let hashCode = 5381;
                for (let i = 0; i < key.length; i++) {
                    hashCode = 33 * hashCode + key.charCodeAt();
                };
                return hashCode % 1013;
            };
            // 存和改
            HashTable.prototype.put = function (obj) {
                let index = this.hash(obj.name);
                // 初始化该位置
                let current = this.items[index];
                // 判断要存储位置是否为空
                if (current == null) {
                    this.items[index] = [];
                    current = this.items[index];
                    // 为空 直接插入到第一个位置
                    current.push(obj);
                } else {
                    // 不为空 判断和里面的元素是否相同
                    for (let i = 0; i < current.length; i++) {
                        // 相同修改
                        if (current[i].name == obj.name) {
                            current[i] = obj;
                            return;
                        }
                    };
                    // 不同 插入到最后
                    current.push(obj);
                }
            };
            // 取
            HashTable.prototype.get = function (str) {
                let index = this.hash(str);
                let current = this.items[index];
                if (current != null) {
                    for (let i = 0; i < current.length; i++) {
                        if (current[i].name == str) {
                            return current[i];
                        }
                    };
                }
                return null;
            };
    };
    // 测试代码  

        let hs = new HashTable();
        // 创建一个对象并存如
        let shuaxin = {
            name: "shuaxin",
            phone: '158****26**'
        };
        hs.put(shuaxin);
// 测试哈希表和数组查找效率对比
// 先给数组空的位置插入模拟数据 100w
        function insert() {
            for (let i = 0; i < 1000000; i++) {
                if (hs.items[i] == null) {
                    hs.items[i] = [{
                        name: "demo",
                        phone: "456"
                    }];
                } else {
                    continue;
                }
            };
        };
        insert();
        // 数组普通的对比查找
        function find(str) {
            // console.log(str);
            for (let i in hs.items) {
                if (str == hs.items[i][0].name) {
                    return hs.items[i][0];
                }
            };
            return null;
        };

        // 上文图片的结果
        console.warn('100w数据中效率对比');
        // 250+ms
        console.time('遍历查找');
        console.log(find('shuaxin'));
        console.timeEnd('遍历查找');
        // 1ms左右
        console.time('哈希查找');
        console.log(hs.get('shuaxin'));
        console.timeEnd('哈希查找');
```