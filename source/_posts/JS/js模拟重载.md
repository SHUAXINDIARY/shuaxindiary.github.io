---
title: 关于js实现重载
category:
  - 笔记
tag:
  - JavaScript
  - 前端
abbrlink: 38655
date: 2019-07-23 10:05:48
---
# 本文源码 [这里](https://github.com/SHUAXINDIARY/demo/blob/master/js%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/%E6%A8%A1%E6%8B%9F%E9%87%8D%E8%BD%BD.html)

# 重载是什么

    定义：函数名相同,函数的参数列表不同(包括参数个数和参数类型)，至于返回类型可同可不同


<!-- more -->
# js没有实现重载

```javascript
    let calculate={
        add(a){
            console.log(a);
        },
        add(a,b){
            cosole.log(a+b);
        }
    };
    calculate.add(5);// NaN  因为第二个参数未定义
    calculate.add(5,5);// 10
```

    根据上面的例子发现，当有两个重名的方法的时候，会执行后面的那一个

# 模拟重载
虽然js本身不支持重载，但是我们可以通过 [arguments对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)和[rest参数](http://es6.ruanyifeng.com/?search=rest&x=0&y=0#docs/function#rest-%E5%8F%82%E6%95%B0)来模拟重载的效果


# 代码演示

arguments对象版本

```javascript
let calculate = {
    // arguments版本
    add_arguments() {
        console.log(`arg是数组吗:${arguments instanceof Array}`);
        let argCount = arguments.length;
        let sum = 0;
        switch (argCount) {
            // 当传入一个参数的时候，直接返回该值
            case 1:
                sum = arguments[0];
                console.log(`sum=${sum}`);
                break;
                // 当参数个数大于一个时，进行求和
            default:
                for (let i in arguments) {
                    sum += arguments[i];
                }
                console.log(`sum=${sum}`);
                break;
        };
        return sum;
    }
};
 calculate.add_arguments(1);//1
 calculate.add_arguments(1, 1, 100);//102
```

rest参数版本

```javascript

let calculate={
    // rest参数版本
    add_rest(one, ...arg) {
        console.log(`arg是数组吗:${arg instanceof Array}`);
        let sum = 0;
        // 当传入一个参数的时候，直接返回该值
        if (arg.length == 0) {
            console.warn('参数只有一个');
            console.log(one);
            sum = one;
        } else {
            // 当参数个数大于一个时，进行求和
            console.log(`参数有${arg.length+1}个`);
            sum = one;
            for (let i in arg) {
                console.log(arg[i]);
                sum += arg[i];
            };
            console.log(sum);
        };
        return sum;
    }
};
calculate.add_rest(0);//0
calculate.add_rest(0,10);//10
```