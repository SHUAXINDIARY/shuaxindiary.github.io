---
title: vue原理学习1-响应式
category:
  - 笔记
tag:
  - VUE
  - 前端
abbrlink: 309
date: 2020-05-02 11:01:38
---

# 开头
- 前几天看到vue3.0的beta版本出来了，觉得在正式版出来之前，还是得把2.x版本的原理过一遍，让脑子有个印象。
- 大概了分了这么几个部分：响应式系统、观察者模式、模板编译、virtual dom、路由、vuex、vue插件开发

<!-- more -->

# 响应式
>概念
- 针对某个参照物，进行相对变化。

>解释
- vue这类框架的响应式和bootstrap的响应式表现形式不一样，但是意思都是一样的，基于某个参考物进行相对变化。
- bootstrap是针对显示设备的大小来对布局进行自动的调整；vue是针对数据，对视图进行自动的更新，所以vue也是数据驱动型框架
- 举个例子：当前有个标签显示的内容是：***刷新*** ，换jq时代我们需要去手动获取该标签，然后把值为 ***刷新*** 的这个变量插入进去；假设现在我们改变了这个变量的值，我们需要让页面同步变化，就需要重复上面操作,代码如下
```html
...
<body>
    <div id="name"></div>
    <script>
        let div = document.querySelector('#name')
        let name = "刷新"
        // 渲染数据到页面
        let render = (dom, val) => {
            dom['textContent'] = val
        }
        // 初始化
        render(div, name)
        // 修改name后再次渲染
        name = "shuaxin"
        render(div, name)
    </script>
</body>
...
```
- 上述代码在要操作的dom变多的时候，维护起来就会很麻烦，而换到vue这边，就简单的多，只需要告诉vue数据显示在哪里即可，至于怎么去显示到对应的dom里，以及数据发生变化后的更新页面显示，都是由vue来完成的
```html
...
<body>
    <div id="app">
        {{name}}
    </div>
    <script src="./vue.js"></script>
    <script>
        let app=new Vue({
            el:"#app",
            data:{
                name:"刷新"
            }
        })
    </script>
</body>
...
```

# 关于vue的工作流程
在通过Vue构造函数实例化对象的过程中，vue同时做了2件事

>1.数据拦截
- 对用户传入的数据，即data的所有属性进行拦截，以此来实现vue的响应式

>2.编译
- 对用户指定的目标节点下的内容进行解析，解析出普通节点、包含指令的节点、含有表达式的节点；
- 编译的目的，是为了生成虚拟dom，即用js对象来描述dom节点，以此来优化操作dom时带来的性能问题
- 具体流程为：在每次编译完后，vue都会生成一棵由vdom组成的dom树，然后来和上一次的dom树进行对比，找出其中的不同，然后只更新其中不同的地方到页面

>数据驱动
- 在完成1，2后， vue会通过观察者模式，来链接数据和编译；达到每次更新数据后，自动去更新页面的效果
- 具体流程是，为拦截的每个数据创建一个容器，负责装载用到该数据时创建的watcher，这个watcher负责去执行更新页面的操作；然后当每次数据发生改变的时候，就会去遍历该数据的watcher容器，执行里面每一个watcher更新页面的操作

# 关于响应式的原理
- 对对象：vue2.x通过数据拦截api（Object.defineProperty）来实现这一功能
- 对数组：数据拦截是object的api，所以对数组那些操作数组本身内容的api就没什么用了，vue的做法是对数组的这些api进行一层封装，在里面执行数据依赖的一些操作和该api本身的操作

# 代码实现

```js
// 可以操作数组本身内容的api
const methodsToPatch = [
            'push',
            'pop',
            'shift',
            'unshift',
            'splice',
            'sort',
            'reverse'
]
// 创建一个新的数组对象，继承Array
let newArr = Object.create(Array.prototype)
// 遍历methodsToPatch，拦截每一个方法
methodsToPatch.forEach((method) => {
    // 保存的是原Array的对应调用的方法
    let oldMethod = newArr[method]
    Object.defineProperty(newArr, method, {
        // 不可以枚举，不可被for in循环到
        enumerable: false,
        // 可以被删除
        configurable: true,
        // 可被重写
        writable: true,
        // 将访问的元素换成函数，在这里执行通知watcher更新视图的操作
        value: function mutator(...rest) {
            // (3) [1, 2, 3]  4
            console.log(this, ...rest)
            // 将函数的this指向绑定到当前操作的数组上，然后把调用该函数的参数一并传递过去
            let res = oldMethod.apply(this, rest)
            return res
        }
    })
})
// 测试
let data = {
    name: "vuejs",
    sx: {
        sex: '男',
        name: "shuaxin"
    },
    arr: [1, 2, 3]
}
// 实现对数据的拦截
let defineReactive = (obj, key, value) => {
    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get() {
            // 下面代码替代增加watcher操作
            console.warn("get");
            return value
        },
        set(newVal) {
            // 以下代替通知watcher更新视图操作
            console.warn("set");
            value = newVal
        }
    })
}
// 判断传递的data的每个属性的类型
// 这里会有三种类型:对象,基本数据类型,数组
let observe = (obj) => {
    if (!obj || (typeof obj !== 'object')) {
        return
    }
    Object.keys(obj).forEach((key) => {
        // 还是一个对象，递归调用该函数
        if (typeof obj[key] === 'object' && (obj[key] instanceof Array) !== true) {
            console.warn("对象");
            observe(obj[key])
        } else if (obj[key] instanceof Array) {
            // 是数组的话，改变数组原型为我们新创建的数组对象newArr
            console.warn("数组");
            obj[key].__proto__ = newArr
            // 对内部的元素执行拦截
            defineReactive(obj, key, obj[key])
        } else {
            // 基本数据直接进行拦截
            console.warn("基本数据类型");
            defineReactive(obj, key, obj[key])
        }
    })
}
// 初始化
observe(data)
// 执行该操作的时候，控制台会打印 get
data.arr.push(4)
console.log(data.arr);
```