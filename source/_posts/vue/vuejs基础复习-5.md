---
title: vuejs基础复习-5
categories:
  - 技术笔记
tag:
  - VUE
  - 前端
abbrlink: 38099
date: 2020-02-10 14:12:58
---

# 路由
- 概念：
>根据URL决定显示什么内容，这个系统就是路由系统。

- 为什么需要
>因为vue和react这些SPA框架，只有一个html页面，所以需要使用某种手段来决定当前页面显示什么内容，所以才有了前端路由系统。
<!-- more -->

# 基本使用
- 路由配置除外，使用到的只有2个标签
```html
<!-- 用于进行页面的跳转 -->
<router-link />
<!-- 用于显示，根据url决定显示的页面 -->
<router-view></router-view>
```
- 进行切换页面跳转
>在浏览器输入http:****/home就会显示home ，输入about就会显示about

```html
<!-- Home.vue -->
<template>
    <div>
        home
    </div>
</template>
<script>
export default {
    name:'home'
}
</script>
<!-- About.vue -->
<template>
    <div>
        about
    </div>
</template>
<script>
export default {
    name:'about'
}
</script>
```
```js
// 在router.js中配置需要显示的页面文件
...
const routes=[
    {
        // url的路径
        path:'/home',
        // home.vue文件的name
        name:'home',
        // 引入该文件
        component:()=>import('Home.vue')
    },
    {
        path:'/about',
        name:'about',
        component:()=>import('About.vue')
    },
]
...
```

- 动态路由显示
>例如一个有一个todo的列表，其中每一项内都有不同的数据，但是数据显示的样式什么都一样，我们可以用同一个组件来渲染，只需要在跳转时传递一个标识，来告诉组件加载所对应的数据就可以了

```js
// 在router.js中配置需要显示的页面文件
...
const routes=[
    {
        // url的路径
        path:'/home',
        // home.vue文件的name
        name:'home',
        // 引入该文件
        component:()=>import('Home.vue')
    },
    {
        name:'list/:id,
        component:'list',
        component:()=>import('List.vue')
    }
]
...
```
```html
<!-- home.vue -->
<template>
    <div>
        <ul>
            <li v-for="(item,index) in info" :key="index">
                <!-- 切换url来进行页面跳转，类似a标签 -->
                <!-- 跳转时携带点击项的id -->
                <!-- 这里也可以使用query替代params -->
                <router-link :to="{name:'list',params:{id:'item'}}">
                    {{item.id}}
                <router-link>
            </li>
        </ul>
    </div>
</template>
<script>
export default {
    name:'list',
    data(){
        return{
            info:[
                {
                    id:'1'
                },
                {
                    id:'2'
                }
            ]
        }
    }
}
</script>
<!-- list.vue -->
<template>
    <div>
        <p >
            {{id}}
        </p>
    </div>
</template>
<script>
export default {
    name:'list',
    data(){
        return{
            info:''
        }
    },
    mounted(){
        // 接受从home组件传递过来的id
        // 如果跳转那里使用的query这里的params也需要换成query
        this.info=this.$route.params.info;
    }
}
</script>
```
>query和的区别：query使传递的值会显示在url中，params不会，所以这也导致了假如我们没有在list后面写id，那list页面刷新后，传递过来的数据就会丢失。
>总结：使用query可以不在路由进行配置，params时要在路由进行动态参数配置

# 其他使用
总结除过页面跳转意外，使用过的路由系统的功能。
- 路由嵌套
>使用场景：当一个页面下的其他页面都有相同的部分，只有小部分需要根据不同的地址来显示不同的内容时，就可以使用，比如后台管理系统，所有页面左边都有整体的菜单
```js
// router.js配置
...
const routes=[
    {
        // url的路径
        path:'/home',
        // home.vue文件的name
        name:'home',
        // 引入该文件
        component:()=>import('Home.vue'),
        // 配置 子路由
        children:[
            {
                path:'/one',
                name:'one',
                component:()=>import('One.vue'),
            },
            {
                path:'/two',
                name:'two',
                component:()=>import('Two.vue'),
            },
            ...
        ]
    }
]
...
```
```html
<!-- home.vue使用 -->
<template>
    <div>
        <p >
            菜单
        </p>
        <!-- 根据url的不同来显示one，two -->
        <router-view />
    </div>
</template>
<script>
export default {
    name:'home',
}
</script>
```

- 路由导航守卫

>作用
>- 在页面进入前后进行一些操作，比如重定向，数据操作等等

>使用场景
>- 在页面进入前验证token是否过期、验证用户是否还有权限访问该页、根据需要不一样，重新导向用户的访问页面

>分类:
>>全局：作用在所有路由
>>路由独享：只作用在配置的路由
>组件内：只作用在配置的组件
