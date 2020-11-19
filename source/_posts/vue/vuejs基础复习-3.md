---
title: vuejs基础复习3---vuex
categories:
  - 技术笔记
tag:
  - Vue
  - 前端
abbrlink: 25776
date: 2019-10-06 18:22:27
---

# 基本概念
>1.在每一个组件内部，都有一份属于该组件的data，页面显示的数据也依赖于此，我们也可以通过函数等来改变data中的数据，整个流程就像下图一样
![vuex1](/img/vuex1.png)
- state相当于组件的data
- view相当于显示data数据的html结构
- action相当于一些事件触发的函数

<!-- more -->
>2.通过action改变state的数据，view来实时显示state中的数据
>3.vuex就是一个所有组件一起维护的一个data
>4.因为3，所以他是最强大的组件通信方式

# 使用
>核心概念
- State-------相当于组件的data存放具体数据
- Getters-----相当于组件的计算属性
- Mutations---存放更改state中数据的事件(就是一个一个的函数)
- Actions-----用来处理异步操作的事件
- Module------对state数据进行模块化划分 
![vuex2](/img/vuex2.png)
>通过commit触发mutations的事件来改变state的数据，组件显示state的数据
>当有异步操作时，通过dispatch触发actions内部的事件，然后传递给mutations的事件，在改变state的数据

>代码演示
- 使用vue-cli创建一个项目(目录如下图)
![项目目录](/img/vuex3.png)
- store中存放我们的vuex代码(store.js)

>代码实现
- store.js

```javascript
const store=new Vuex.Store({
    state:{},
    getters:{},
    mutations:{},
    actions:{},
    modules:{}
})
```
## store使用
>***在A组件中获取state的数据并显示*** 
- A组件($store.state.name---获取vuex的数据)
```html
<template>
    <div>
        {{$store.state.name}}
    </div>
</template>
<script>
export default{
    name:'A'
}
</script>
```

- store.js
```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    getters:{},
    mutations:{},
    actions:{},
    modules:{}
})
```
## getters使用1
>**获取数据后添加一段字符串在显示** 
- A组件
```html
<template>
    <div>
        {{$store.getters.addStr}}
    </div>
</template>
<script>
export default{
    name:'A'
}
```

- store.js
```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    // 默认参数1 state， 等同于this.state
    // 默认参数2 getters 等同于this.getters
    getters:{
        addStr(state){
            return state.name+'111';
        }
    },
    mutations:{},
    actions:{},
    modules:{}
})
```
## getters使用2
>**获取数据后添加一段字符串在显示，添加的字符串是组件传递过去的** 
- A组件
```html
<template>
    <div>
        {{$store.getters.addStr('A组件')}}
    </div>
</template>
<script>
export default{
    name:'A'
}
```

- store.js

```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    getters:{
        addStr(state){
            // 通过返回一个函数 然后接受组件传递的参数
            return function(str){
                return state.name+str;
            }
        }
    },
    mutations:{},
    actions:{},
    modules:{}
})
```
## mutations使用
>**当点击按钮后，改变state中的name** 
- A组件
```html
<template>
    <div>
        {{$store.state.name}}
    </div>
    <button @click='change'>btn</button>
</template>
<script>
export default{
    name:'A',
    methods:{
        change(){
            // 1.通过commit(事件类型)触发mutations的事件
            this.$store.commit('changeName');
            // 2.通过对象类型触发
            this.$store.commit({
                // 触发事件类型
                type:'changeName',
                // 传递的参数
                num:0
            });
        }
    }
}
```

- store.js
```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    getters:{},
    mutations:{
        // 默认参数1.state
        // 默认参数2.payload 这个参数是接受触发该事件时传递的参数
        // 注:如果是以对象格式触发该事件时，payload接受到的是一个对象
        changeName(state,payload){
            state.name='mutations使用';
        }
    },
    actions:{},
    modules:{}
})
```
## actions使用
>**当点击按钮后，先发送actions，进行异步操作后，触发mutations的事件，在改变state的name** 
- A组件
```html
<template>
    <div>
        {{$store.state.name}}
    </div>
    <button @click='change'>btn</button>
</template>
<script>
export default{
    name:'A',
    methods:{
        change(){
            this.$sotre.dispatch('asyncChange');
        }
    }
}
```

- store.js
```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    getters:{},
    mutations:{
        // 默认参数1.state
        // 默认参数2.payload 这个参数是接受触发该事件时传递的参数
        // 注:如果是以对象格式触发该事件时，payload接受到的是一个对象
        changeName(state,payload){
            state.name='mutations使用';
        }
    },
    actions:{
        // 默认参数1.context 就是store对象;
        // 默认参数2.payload为接受的参数对象
        asyncChange(context,payload){
            setTimeout(()=>{
                context.commit('changeName');
            },1000);
        }
    },
    modules:{}
})
```
## module的使用
>说明
- 用来模块化state中的数据的
- store.js
-  A组件
```html
<template>
    <div>
        {{$store.state.personInfo.name}}
    </div>
</template>
<script>
export default{
    name:'A',
}
```

```javascript
const store=new Vuex.Store({
    state:{
        name:'Vuex学习'
    },
    getters:{},
    mutations:{},
    actions:{},
    modules:{
        perosonInfo:{
            state:{
                name:'man'
            },
            getters:{},
            mutations:{},
            // 需要注意 模块中的actions的context指向的是该模块
            // 如果想要获取根state:  context.rootstate即可
            actions:{},
            modules:{
        },
        animalInfo:{
            state:{
                name:'dog'
            },
            getters:{},
            mutations:{},
            actions:{},
            modules:{
        }
    }
})
```

# 以上就是vuex基本使用，详细可以参考官方文档


