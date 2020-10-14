---
title: vuejs基础复习2---组件/组件通信
category:
  - 笔记
tag:
  - VUE
  - 前端
abbrlink: 47404
date: 2019-09-26 11:26:06
---


# 什么是组件呢？
>1.简单的理解，UI层组件可以是一个按钮，可以是一个轮播图等等；逻辑方面，就是可复用的代码块；
>2.更简单粗暴的理解，因为vue做的是单页应用，意味着只有一个HTML文件，那每一个页面其实就是组成这个HTML文件的一部分，而每个页面又是通过不同的部分组成；所以一个页面可以理解成一个大的组件，页面里的每一个部分，都是一个一个小组件；
<!-- more -->
# 什么是组件化开发和模块化开发呢？
>1.模块化和组件化想要达成的目的都是一样的，组件化是包含了UI和页面逻辑处理在一起；而模块化只是将可以重复使用的逻辑代码提取成一个个模块，方便随时使用；
>2.两者都是一种开发思想，为了更好的开发效率
<!-- more -->

# 怎么在vue中创建并注册一个组件呢？(暂时不包括模块开发)
>1.在vue中全局创建组件
```html

<!-- 第一个参数 是字符串，定义该组件名字； -->
<!-- 第二个参数是一个对象，需要定义组件的模板，其他都是和vue实例中的一样 -->
<!-- 这里需要注意，一个组件的 data 选项必须是一个函数；
这样的目的，是为了让每个组件之间的数据独立；
原理就是js函数会重新开辟一块存储空间，存储当前组件的数据
返回一个对象就是让我们能拿到这个对象里的数据 -->
<script>
    Vue.component('btn',{
        template:'<button @click="hint">clickme</button>',
        data(){
            return{
                name:'全局组件'
            }
        },
        methods:{
            hint:function(){
                alert(this.name);//在页面会出现提示窗 并显示全局组件
            }
        }
    });
</script>
// 使用该组件
<btn></btn>
```
>2.创建一个局部组件
```html

// 注册后才能使用
<div id='app'>
    <btn></btn>
</div>

<script>
let btn={
    template:'<button @click="hint">clickme</button>',
    data(){
        return{
            name:'局部组件'
        }
    },
    methods:{
        hint:function(){
            alert(this.name);//在页面会出现提示窗 并显示全局组件
        }
    }
};
new Vue({
    le:'#app',
    data:{
        name:'组件练习'
    },
    // 注册我们的局部组件
    components:{
        btn:btn
    }
})
</script>
```
>3.两种组件创建的区别：
- 全局组件不需要注册，可以直接使用，所以可以再任意的vue实例中使用；
- 局部组件需要在要使用它的vue实例里注册后，才能使用

# 什么是组件通信？
>在每一个组件内部，都有一份属于该组件的data，页面显示的数据也依赖于此，我们也可以通过函数等来改变data中的数据
>正因为每一个组件维护属于自己的data，所以在一个组件中想要使用其他组件的data数据就需要进行组件通信

# 组件通信有哪些类型呢？
>1.父子组件之间传递
>2.子父组件之间传递
>3.兄弟组件之间传递
>4.跨级组件之间传递

# 关于父子组件通信
>1.在父组件中使用子组件时，在子组件上通过v-bind(缩写为 :)绑定要传递的数据
>2.在子组件中，使用props来接受父组件传递过来的数据

***代码***

```html

<!-- 这里父组件就是我们的vue实例 然后创建一个子组件one -->
<div id='app'>
<!-- 绑定我们要传递的数据，然后传递要传递的数据 -->
    <one v-bind:name='name'></one>
</div>
<script>
let one={
    template:'<div>{{name}}</div>',
    props:{
        name:{ type:String}
    }
};
new Vue({
    le:'#app',
    data:{
        name:'父组件'
    },
    // 注册我们的局部组件
    components:{
        one:one
    }
})
</script>
```

# 关于子父组件通信
>1.子组件通过$emit监听一个自定义事件，传递数据给父组件
>2.父组件通过v-on(缩写为@)监听该自定义事件，然后该事件会触发一个函数来获取子组件传递的数据

```html
<div id="app">
    {{name}}
    <one v-on:ok='get'></one>
</div>
<script>
// 点击子组件的button 后开始传递数据
 let one={
        template:'<div><button @click="start">click</button></div>',
        data(){
            return{
                name:'子组件'
            }
        },
        methods:{
            start:function(){
                // 触发自定义ok事件
                this.$emit('ok',this.name);
            }
        }
    }
new Vue({
    el:'#app',
    data:{
        name:'parent'
    },
    components:{
        one
    },
    methods:{
        get:function(e){
            this.name=e;
        }
    }
})
</script>
```

# 使用全局事件管理中心进行跨级组件通信(父子,子父,跨级也可以用)
>1.重新创建一个vue对象
>2.在需要传递参数的组件内部通过$emit绑定触发一个自定义事件，并传递要传递的数据
>3.在需要接受数据的组件通过$on绑定被emit触发的事件，然后在回调函数中接受传递过来的参数

```html
    <div id="app">
        <one></one>
        <two></two>
    </div>
    <script>
    // 创建事件管理中心对象
    let event=new Vue();
    // 创建两个组件
    let one={
        template:'<div>{{name}}<button @click="start">发送</button></div>',
        data(){
            return{
                name:'one'
            }
        },
        methods:{
            // 当我们点击发送按钮后执行该函数
            start(){
                // 触发recive这个我们自定义的事件 并传递要传递的数据
                event.$emit('recive',this.name);
            }
        }
    }
    let two={
        template:'<div>{{name}}</div>',
        data(){
            return{
                name:'two'
            }
        },
        // 官方的生命周期函数  理解为在我们的所有数据渲染在div节点内部前自动执行的一个函数
        mounted(){
            // 这里我们监听另一个组件触发的函数，然后接受传递过来的数据
            event.$on('recive',(data)=>{
                this.name=data;
            });
        }
    }
    new Vue({
        el:'#app',
        data:{
            name:'event'
        },
        components:{
            one,
            two
        }
    })
    </script>
```

# 跨级组件(父组件和子孙组件)通信
***假设父组件是A，子组件是B，孙子组件是C***
>1.A组件通过v-bind绑定要传递数据
>2.在B中不使用props接收，而是在B组件中的C组件上通过v-bind='$attrs' 来转发A组件传递过来的数据
>3.在C组件内部可以通过this.$attrs获取到A组件传递过来的数据(获取到的是对象格式)
>tips:
- 在C组件中设置inheritAttrs: false；
- 这个属性是为了C组件在B中使用v-bind='$attrs'时，该属性不会被当做HTML的属性渲染到页面

```html
    <div id="app">
            <one :name='name'></one>
    </div>
    <script>
    let two = {
                template: '<div>{{name}}</div>',
                data() {
                    return {
                        name: 'two'
                    }
                },
                mounted() {
                    // 接受传递的数据
                    this.name = this.$attrs['name'];
                },
                inheritAttrs: false,

    }
    let one = {
        // 转发父组件的数据给自己的子组件
        template: '<div>{{msg}} <two v-bind="$attrs"></two> </div>',
        data() {
            return {
                msg :'one'
            }
        },
        components: {
            two
        }
    }
    new Vue({
        el: '#app',
        data: {
            name: 'app'
        },
        components: {
            one
        }
    })
    </script>
```

# 使用provide(对象/函数内部返回一个对象)和inject(数组/字符数组格式)进行跨级通信
>1.在父组件通过provide提供要所有子组件可继承的属性
>2.所有的子组件都可以用inject来进行继承
```html
    <div id="app">
            {{name}}
            <one></one>
    </div>
    <script>
        let one = {
            template: '<div>{{msg}} </div>',
            data() {
                return {
                    msg: 'one'
                }
            },
            inject: ['name'],
            mounted() {
                console.log(this.name);
                this.msg = '继承的'+this.name;
            }
        }
        new Vue({
            el: '#app',
            data: {
                name: 'app'
            },
            provide() {
                // 定义一个name属性 然后把data的name的值赋值给他
                this.name = this.name;
                return {
                    // 返回一个新的name变量 值为刚才定义的name属性
                    name: this.name
                }
            },
            components: {
                one
            }
        })
        </script>
```

# $parent和$childern和ref进行父子组件通信
>获取子组件数据
- 在子组件绑定ref='属性名'  获取时通过this.$refs.属性名 获取到该组件实例
>获取父组件
- this.$parent
```html
<div id="app">
        {{name}}
        <one ref='one'></one>
</div>
<script>
    let one = {
        template: '<div>one</div>',
        data() {
            return {
                name: 'one'
            }
        },
        mounted(){
            // 获取父组件的data
            console.log(this.$parent.name);
        }
    }
    new Vue({
        el: '#app',
        data: {
            name: 'app'
        },
        components: {
            one
        },
        mounted() {
            // 获取子组件data
            console.log(this.$refs.one.name);
        }
    })
    </script>
```
