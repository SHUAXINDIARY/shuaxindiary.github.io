---
title: '关于call,bind,apply'
category:
  - 笔记
tag:
  - JavaScript
  - 前端
abbrlink: 21962
date: 2019-11-20 19:23:11
---
# 关于call,bind,apply的作用
这三个方法都是只有函数才可以调用，作用就是改变函数执行时this的指向。
<!-- more -->
# 代码中用到的一些知识的说明
>***rest***
- rest是es6的一个新语法，参数接受函数的多余参数，组成一个数组，放在形参的最后
```js
let one=function(...rest){
    console.log(rest instanceof Arrary); // true
    console.log(rest);// [1,2,3]
};
one(1,2,3);
```

> ***...<一个数组>*** 
- 解构赋值,他是一种针对数组或者对象进行模式匹配，然后对其中的变量进行赋值
```js
let arr=[1,2,3];
console.log(arr);// [1, 2, 3]
console.log(...arr);// 1 2 3
let brr=[...arr];
// 很方便的复制arr的值到brr
console.log(brr);// [1, 2, 3]
```

# 如何使用?
call
>***参数(obj,arg1,arg2,...)***
- 第一个参数是要绑定的目标对象，后面的参数就是要传入函数内的参数，可以是任何类型
- 代码示例
```js
let one = {
    name: 'one',
    sayhi(...rest) {
        console.log(`这是${this.name}的函数,这是传递进来的参数：${rest[0]},${rest[1]}`);
    }
};
// 这是one的函数,这是传递进来的参数：参数一,参数二
one.sayhi('参数一', '参数二');
let two = {
    name: 'two'
};
// 这是two的函数,这是传递进来的参数：参数1,参数2
one.sayhi.call(two, '参数a', '参数b');
```

bind
>***参数(obj,arg1,arg2,...)()***
- 第一个参数是要绑定的目标对象，后面的参数就是要传入函数内的参数，可以是任何类型
- 使用时，需要在调用一次，因为bind返回的是一个函数
- 代码示例
```js
let one = {
    name: 'one',
    sayhi(...rest) {
        console.log(`这是${this.name}的函数,这是传递进来的参数：${rest[0]},${rest[1]}`);
    }
};
// 这是one的函数,这是传递进来的参数：参数一,参数二
one.sayhi('参数一', '参数二');
let two = {
    name: 'two'
};
// 这是two的函数,这是传递进来的参数：参数1,参数2
one.sayhi.bind(two, '参数a', '参数b')();
```

apply
>***参数(obj,[arg1,arg2,...])***
- 第一个参数时要绑定的目标对象,后面的参数，都以数组形式传入
```js
let one = {
    name: 'one',
    sayhi(...rest) {
        console.log(`这是${this.name}的函数,这是传递进来的参数：${rest[0]},${rest[1]}`);
    }
};
// 这是one的函数,这是传递进来的参数：参数一,参数二
one.sayhi('参数一', '参数二');
let two = {
    name: 'two'
};
// 这是two的函数,这是传递进来的参数：参数1,参数2
one.sayhi.apply(two, ['参数a', '参数b']);
```

# 总结三者不同
>传参数不同
- call和bind在参数传递时，都是以多参数传递，只是bind使用是需要再调用一次返回的函数；
- apply在传递参数时，都是以数组形式传递；

>使用不同
- call和apply改变了函数的this上下文后便执行该函数
- bind则是返回改变了上下文后的一个函数。

# 三者的应用
>***将伪数组转换成数组***
- 啥是伪数组?就是长得像数组，但是不是数组，是一个对象；以及含有length的对象
```js
// 函数内部的 arguments 就是一个伪数组
let one = function () {
    console.log(arguments); //Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
    console.log(typeof arguments); //object
    console.log(arguments instanceof Array); // false
    let arr = Array.prototype.slice.call(arguments);
    console.log(arr); //  [1, 2, 3]
    console.log(typeof arr); // object
    console.log(arr instanceof Array); //true
};
one(1, 2, 3);
```

>***利用call和apply实现继承***
```js
function Person(name) {
    this.name = name;
    this.sayhi = function () {
        console.log(`${this.name}向您问好`);
    }
}

function Man(name) {
    Person.call(this, name);
}
let shuaxin = new Man('刷新');
shuaxin.sayhi();
```

# 来动手实现一下call、bind、apply
call
```js
// 通过rest来接受参数
Function.prototype.mycall = function (...rest) {
    // 判断调用该函数的是不是函数
    if (typeof this === 'function') {
        // 获取要绑定的对象
        var obj = rest.splice(0, 1)[0];
        // 给目标对象添加当前调用的函数
        obj[this.name] = this;
        // 并将要传递参数再一次传入进去
        obj[this.name](...rest);
        // 删除目标对象的该方法
        delete obj.sayhi;
    } else {
        console.log('该函数只可以被函数调用');
    }
    return obj[this.name];
};
```

bind
```js
Function.prototype.mybind = function (...rest) {
    if (typeof this === 'function') {
        // 获取要绑定的对象
        var obj = rest.splice(0, 1)[0];
        var fname = this.name;
        // 给该函数绑定当前调用的函数
        obj[fname] = this;
    } else {
        console.log('该函数只可以被函数调用');
    }
    return function () {
        // 将要传递参数再一次传入进去
        obj[fname](...rest);
        // 删除该方法
        delete obj[fname];
    }
};
```

apply
```js
 Function.prototype.myapply = function (...rest) {
    if (typeof this === 'function') {
        // 获取要绑定的对象
        var obj = rest.splice(0, 1)[0];
        // 给该函数绑定当前调用的函数
        obj[this.name] = this;
        // 将要传递参数再一次传入进去
        if (rest[0] instanceof Array) {
            obj[this.name](...rest[0]);

        } else {
            obj[this.name](rest[0]);
        }
        // 删除该方法
        delete obj.sayhi;
    } else {
        console.log('该函数只可以被函数调用');
    }
    return obj[this.name];
};
```
# 参考链接
[JavaScript 中 call()、apply()、bind() 的用法](https://www.runoob.com/w3cnote/js-call-apply-bind.html)

[JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)

[让你弄懂 call、apply、bind的应用和区别](https://juejin.im/post/5a9640335188257a7924d5ef)
