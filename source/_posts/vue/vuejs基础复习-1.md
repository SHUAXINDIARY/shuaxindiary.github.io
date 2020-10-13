---
title: vuejs基础复习1---指令/基本语法
category:
  - 笔记
tag:
  - VUE
  - 前端
abbrlink: 11937
date: 2019-09-03 21:54:51
---

# 学习前知识储备
1.HTML+CSS+JS肯定要会啊！！！
2.ES6知识储备！

# 一些概念解释

>***什么是MVVM***
>MVVM是一种设计模式，是Model-View-ViewMode的缩写。view就是视图层，可以认为就是HTML。Model是数据层，就是我们通过ajax请求后端接口获取到的数据。ViewMode就是vue帮我们做的，将我们的数据绑定到我们要显示的视图层上。

<!-- more -->
>***什么是数据驱动***
>MVVM框架的特点，当数据变化时，视图层显示也会自动变化；反过来也是一样。

>***为什么要用vue(或者类似的框架)？***
>1.减少了对dom的操作,我们只需要关心我们的数据，dom操作交给框架就好
>2.让前后端分离更彻底

>***什么SPA应用？***
>全称是单页Web应用（single page web application，SPA），只有一个HTML页面，通过访问路径不同来切换显示内容。
>之前纯HTML+CSS的开发，通过a标签跳转的，有多个页面的就是多页应用。

>***路由***
>路由是根据不同的 url 地址展示不同的内容或页面

>前后端路由区别?
>1.前端路由完全由前端工程师控制，通过不同的URL俩决定显示区域显示的内容，页面不会刷新
>2.后端路由是浏览器地址栏切换不同url时，每次都会给后台发送一个请求，然后后端在根据请求返回对应的页面，页面会刷新。

>***什么叫组件？***
>1.你可以把它理解成某一个页面，或者某个页面的一部分，比如一个按钮等。
>2.我们开发中需要大量重复利用的我们就可以封装成一个共用组件，来重复使用。


# 正篇

***1.以上的概念作为了解即可，先不管什么是单页应用和组件，慢慢就会了解***
***2.多看vue官方的教程！！！***


>***首先学习vue的基本语法(指令)***
>1.引入vuejs,下载或者引入cdn到你的HTML文件
>2.创建一个vue应用
```html
    <div id="app">
    <!-- 使用我们的name变量  这时打开页面 hello world -->
        {{name}}
    </div>
    <!-- 通过CDN的方式引入vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script>
        // 书写我们的vue 应用代码
        new Vue({
            el:"#app",//绑定到我们的视图容器
            //data对象中 存放我们的所有数据
            data:{
                name:"hello world"
            }
        })
    </script>
```
>3.v-if,v-else条件渲染

```html
    <div id="app">
        <p v-if="on">yes</p>
        <p v-else>no</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data: {
                on: true//on是true时，页面显示yes 反之显示no    
            }
        })
    </script>
```
4.绑定js的原生事件，v-on:加原生js事件名称(以点击事件举例),可以缩写成@加原生js事件名
```html
    <div id="app">
        <button @click="hint">点我</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            // methods中存放我们的函数
            methods:{
                // es6中 当键和函数名一样时 可以省略键名
                hint(){
                    alert("你好");
                }
            }
        })
    </script>
```
5.当在html标签中想使用data中的数据时，用v-bind，可以缩写成 :
```html
    <div id="app">
       <img v-bind:src="src"></img>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data:{
                src:"图片路径"
            }
        })
    </script>
```
6.双向绑定数据，v-model
```html
    <div id="app">
       <!-- 输入的数据保存到msg -->
        <input type="text" v-model="msg">
       <!-- 显示我们msg的数据 我们可以看到当我们在input输入或删除一些文字时，p标签内容会变化 -->
        <p>{{msg}}</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data:{
                msg:"12132"
            }
        })
    </script>
```
7.动态给html绑定class
```html
    <style>
        .con{
            color:red;
        }
    </style>
    <div id="app">
    <!-- 当isActive值为true时，给p标签绑定了一个叫con的class isActive为false时不绑定 -->
       <p v-bind:class="{con:isActive}">测试内容</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data:{
                isActive:true
            }
        })
    </script>
```
8.给html绑定多个class
```html
    <style>
        .con{
            color:red;
        }
        .con2{
            font-size:100px;
        }
    </style>
    <div id="app">
    <!-- 当isActive值为true时，给p标签绑定了一个叫con的class isActive为false时不绑定 -->
       <p v-bind:class="[one.two]">测试内容</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data:{
                one:"con",
                two:"con2"
            }
        })
    </script>
```
9.computed计算属性
>他的作用和一个函数效果是一样的，但是计算属性会缓存我们的计算结果，只有当依赖的数据(实例代码的items)发生改变时，才会重新计算，不然每次调用findSame时，都会使用第一次计算的结果
```html
    <div id="app">
        <!-- 应该显示 [1,1] -->
        <p>{{findSame}}</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.common.dev.js"></script>
    <script>
        new Vue({
            el: "#app",
            data:{
                items:[1,1,2]
            },
            computed{
                findSame(){
                    let arr=[];
                    // 查找item中相同的元素，并且装入到一个新数组中并返回
                    for(let i =0;i<this.items.length;i++){
                        if(arr.indexOf(this.items[i])==-1){
                            arr.push(this.items[i]);
                        }
                    }
                    return arr;
                }
            }
        })
    </script>
```