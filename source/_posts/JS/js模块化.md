---
title: 关于js模块化
category:
  - 技术笔记
tag:
  - JavaScript
  - 前端
abbrlink: 7924
date: 2019-07-21 10:43:20
---


# 前端js模块化规范
1.CommonJS
2.ES6
3.AMD的RequireJS
4.还有其他的，比如CMD的seajs等
<!-- more -->

# CommonJS  [学习更多](http://javascript.ruanyifeng.com/nodejs/module.html)

1.一个文件就是一个模块
2.通过requie导入模块
3.通过exports和module.exports导出模块
4.**主要** 作用在Node(vue-cli脚手架环境里也可以用)
5.CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作
6.引入时，是整体加载引入的文件模块，生成一个对象,然后从该对象里获取内部属性
7.运行时加载

***实例：在node环境中编写***

    ```javascript
        // one.js
        let sayhi=function(){
            console.log('这是one.js文件的sayhi函数');
        };
        /**
            exports.sayhi=sayhi;
        */
        module.exports={
            // es6语法 键值一样时，可以只写一个
            sayhi
        };
        // main.js
        /*真实开发时注意导入路径*/
        let one=require('one.js');
        one.sayhi();//控制台打印 这是one.js文件的sayhi函数
    ```

# ES6  [学习更多](http://es6.ruanyifeng.com/#docs/module)
1.一个文件就是一个模块
2.通过export和export default导出模块

    两个的区别：
        1.  通过export导出的对象属性方法，在导入时也必须安其导出时的名字进行导入
        2.  通过export default 导出的对象属性方法时，
            导入可以任意起名，然后通过该名来调用导入文件内部的成员

3.通过import {***}  from ***导入模块
4.编译时加载，ES6模块不时对象，而是通过export指定输出的代码，再通过import输入
   
***实例：在vue-cli脚手架环境中编写***

    ```javascript
       /**使用export导出  */
        // one.js
        let sayhi=function(){
            console.log('这是one.js文件的sayhi函数');
        };
        export {sayhi};
        // main.js
        /*真实开发时注意导入路径*/
        import {sayhi} from 'one';
        sayhi();//这是one.js文件的sayhi函数
    ```
    ```javascript
        /*使用export default导出  */
        // one.js
        let sayhi=function(){
            console.log('这是one.js文件的sayhi函数');
        };
        export default {
            sayhi
        };
        // main.js
        /*真实开发时注意导入路径*/
        import one from 'one';
        one.sayhi();//这是one.js文件的sayhi函数
    ```
# AMD 的 RequireJS [学习更多](http://javascript.ruanyifeng.com/tool/requirejs.html)

1.在es6出来之前，主要使用就是CommonJS和RequireJS
2.不同于CommonJS的同步加载，RequireJS是异步加载(所以支持回调函数)
3.通过defin定义模块 
4.通过require加载模块(注意require中的依赖是一个数组，即使只有一个依赖，你也必须使用数组来定义))

***实例：引入requirejs后编写***

    ```javascript
        // one.js
            define({
                sayhi(){
                    console.log('这是sayhi');
                }
            });
        // 以上也可以写成
            define(function () {
                return {
                    sayhi() {
                        console.log('这是saihi');
                    }
                }
            });
        // main.js  注意实际开发时的路径
        <script src="https://cdn.bootcss.com/require.js/2.3.6/require.js"></script>
        <script>
            require(['a'],function(a){
                a.sayhi();//这是sayhi
            });
        </script>
    ```
5.当定义模块中依赖其他模块时，把导入的模块当作参数传递进去，再使用

     ```javascript
        // one.js
            define(['./b'],function (b) {
                return {
                    sayhi() {
                        // console.log('这是saihi');
                        b.sayhi();
                    }
                }
            });
        // main.js  注意实际开发时的路径
        <script src="https://cdn.bootcss.com/require.js/2.3.6/require.js"></script>
         <script>
            require(['a'],function(a){
                a.sayhi();//这是sayhi
            });
        </script>
    ```


