---
title: js实现计算器
tag:
  - JavaScript
abbrlink: 998
date: 2019-10-27 18:08:31
category:
---

# 栈计算器
![效果图](/img/computer.png)
<!-- more -->

## 说些废话
>1.界面不是重点,使用的flex布局,仿的ios的计算器界面
>2.代码只写了核心的算法实现(理解了你就能写出来了),想要整体的代码,大家参考给出的源码链接

## [本文源码](https://github.com/SHUAXINDIARY/demo/tree/master/js%E7%BB%83%E4%B9%A0demo/%E8%AE%A1%E7%AE%97%E5%99%A8)

## 分析
>1.多项运算时,运算符优先级的问题
>2.获取要运算的数字时处理,是个位数还是多位数的问题
## 原理
>优先级问题
- 1.用2个栈分别存储数字和运算符
- 2.在存储运算符时判断和前一个运算符的优先级来决定是否要进行运算或者直接存储
- 说明:这里判断优先级,是为了解决一串表达式中间位置出现乘除,如果出现,将这两个数运算后的结果入栈
- 3.因为JS的数组结合push/pop后和栈的效果一致,所以代码使用数组来做

>处理个位数还是多位数问题
- 1.每次获取到的数字暂存在一个变量中,在点击运算符时,在将该变量中的数入栈,随后清空改变量
- 2.当表达式只有两项时,例如 1+2,上述的操作只存储了一个数,所以在点击=号后还需要判断一下,如果该变量不为空,将中间的数入栈.
## 代码实现
```js
// 创建存储数字的栈
let nums = [];
// 存储运算符的栈
let op = [];
// 创建运算符优先级表(用对象来做)
let priority = {
    '+': 0,
    '-': 0,
    '*': 1,
    '/': 1
};
// 比较优先级函数op1栈顶(即数组最后一个)元素,op2为当前获取到运算符
let compare = function (op1, op2) {
    if (priority[op1] > priority[op2]) {
        // 当前运算符优先级小于栈顶元素 取出栈顶运算符和nums栈顶2个数字进行运算 并将结果入栈
        let one = nums.pop();
        let two = nums.pop();
        switch (op.pop()) {
            case '+':
                // 将运算结果入栈
                nums.push(one + two);
                break;
            case '-':
                nums.push(one - two);
                break;
            case '*':
                nums.push(one * two);
                break;
            case '/':
                nums.push(one / two);
                break;
            default:
                break;
        }
        // 将该运算符入栈
        op.push(op2);
    } else {
        // 大于或者等于时 直接入栈
        op.push(op2);
    }
}
// 假设一串表达式是 1+5*6-1;
let str = '1+5*6-1';
let arr = str.split(''); // ["1", "+", "5", "*", "6", "-", "1"]
// 入口函数 
var cal = function () {
    // 模拟真实点击按键获取数据入栈的过程
    arr.forEach(
        (e) => {
            // 判断当前元素是数字还是运算符
            if (isNaN(Number(e))) {
                // 运算符 比较优先级
                compare(op[op.length - 1], e);
            } else {
                // 数字 直接入栈 
                nums.push(Number(e));
            }
        }
    );
    //计算表达式结果   这里没有遵循栈的操作规则 老铁们可以自己优化下 
    // 遍历op 当op内没有元素后 停止循环
    while (op.length != 0) {
        // 依次取出栈低(数组起始位置)的2个元素和符号进行运算
        // 运算完毕后 再将结果插入到第一个 
        let one = nums.shift();
        let two = nums.shift();
        switch (op.shift()) {
            case '+':
                // 将运算结果插入到第一个
                nums.unshift(one + two);
                break;
            case '-':
                nums.unshift(one - two);
                break;
            case '*':
                nums.unshift(one * two);
                break;
            case '/':
                nums.unshift(one / two);
                break;
            default:
                break;
        }
    }
    // 上述操作后 数组最后就剩下一个数 即运算结果
    console.log(nums[0]);
}
cal();
```
# 总结
在最后的计算结果的地方，因为在数组中如果对第一个元素进行增删，当数组元素过多时，会很影响性能，所以这里还有很多优化的地方，后序会再更新。
