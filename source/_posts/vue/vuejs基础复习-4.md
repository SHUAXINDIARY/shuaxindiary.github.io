---
title: vuejs基础复习4---生命周期函数
categories:
  - 笔记
tag:
  - VUE
  - 前端
abbrlink: 50867
date: 2019-10-31 18:35:16
---
# 生命周期函数
vue框架内部提供的一系列函数，主要用于vue创建的实例在挂载到页面以及从页面销毁这一过程中，我们要进行一些操作时所使用的函数。
<!-- more -->

# 所有生命周期函数
beforeCreate
>在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用
```html
...
<body>
    <div id="app">
        {{name}}
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            mthods:{
                sayHi(){
                    console.log('hi');
                }
            },
            beforeCreate() {
                console.log(this.sayHi);// undefined
                console.log(this.name);// undefined
                console.log('beforeCreate');
            }
        })
    </script>
</body>
...
```
created
>在实例创建完成后被立即调用。已经可以获取实例的data、methods、watch。但是不能获取实例根节点
```html
...
<body>
    <div id="app">
        {{name}}
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                sayHi() {
                    return 'hi'
                }
            },
            created() {
                console.log(this.name); // 生命周期函数
                console.log(this.$el);// undefind
                console.log(this.sayHi());// hi
            }
        })
    </script>
</body>
...
```

beforeMount
>这一步生成虚拟dom，准备将其渲染到页面,但还未渲染
```html
<body>
    <div id="app">
        {{name}}
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                sayHi() {
                    return 'hi'
                }
            },
            beforeMount() {
                console.log(this.name); // 生命周期函数
                console.log(this.$el);
                /*  
                    <div id="app">
                        {{name}}
                    </div>
                */
                console.log(this.sayHi()); // hi
                console.log(this.$refs.demo);// undefind
            }
        })
    </script>
</body>
```

mounted
>将虚拟DOM根据el挂载到对应HTML中，将虚拟dom渲染到页面，已经完成
```html
<body>
    <div id="app">
        {{name}}
        <p>dmeo</p>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                sayHi() {
                    return 'hi'
                }
            },
            mounted() {
                console.log(this.name); // 生命周期函数
                console.log(this.$el);
                /*  
                    <div id="app">
                        {{name}}
                    </div>
                */
                console.log(this.sayHi()); // hi
                console.log(this.$refs.demo);// <p>dmeo</p>
            }
        })
    </script>
</body>
```
beforeUpdate
>当data中的数据更新后，虚拟DOM更新前
```html
<body>
    <div id="app">
        <p id="p">
            {{name}}
        </p>
        <button @click='update'>更新</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                update() {
                    this.name = 'update';
                }
            },
            beforeUpdate() {
                console.log(document.getElementById('p').innerText); //生命周期函数
                console.log(1);
            }
        })
    </script>
</body>
```

updated
>数据更新完毕，且虚拟dom完成在页面的渲染之后执行
```html
<body>
    <div id="app">
        <p id="p">
            {{name}}
        </p>
        <button @click='update'>更新</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                update() {
                    this.name = 'update';
                }
            },
            updated() {
                console.log(document.getElementById('p').innerText); //update
                console.log(1);
            }
        })
    </script>
</body>
```
bdeforeDestroy
>当调用vm.$destroy()销毁组件时,销毁前使用,这时data、methods、虚拟dom都可以使用
```html

<body>
    <div id="app">
        <p id="p">
            {{name}}
        </p>
        <button @click='update'>卸载</button>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.js"></script>
    <script>
        let app = new Vue({
            el: '#app',
            data: {
                name: '生命周期函数'
            },
            methods: {
                update() {
                    this.$destroy();
                }
            },
            beforeDestroy() {
                console.log(this.name);//生命周期函数
                console.log(this.$el);//<div id=​"app">​…​</div>​
                console.log(this.update);
               /* ƒ update() {
                    this.$destroy();
                }*/
            }
        })
    </script>
</body>
```
destroyed
>彻底销毁完毕时使用

# 小结
上述就是官方规定的生命周期函数，但大体来看生命周期函数的特点就是会在实例的某个阶段自动调用，这样看watch，computed也应该算。

# 常见使用场景
个人使用过的
>created
- ajax请求

>mounted
- 挂载元素内dom节点的获取

>updated
- 任何数据的更新，如果要做统一的业务逻辑处理

