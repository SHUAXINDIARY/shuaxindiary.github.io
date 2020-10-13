---
title: mongoose简单入门
category:
  - 踩坑
tag:
  - NodeJS
abbrlink: 39885
date: 2019-08-14 09:51:57
---
# mongoose是什么？
Mongoose是MongoDB的一个对象模型工具，是基于node-mongodb-native开发的MongoDB nodejs驱动，可以在异步的环境下执行。同时它也是针对MongoDB操作的一个对象模型库，封装了MongoDB对文档的的一些增删改查等常用方法，让NodeJS操作Mongodb数据库变得更加灵活简单。
<!-- more -->
# 基本增删改查使用(最近几天用过的一些基本的操作) [中文文档](http://www.mongoosejs.net/docs/index.html)
1.安装mongoose
```javascript
npm i mongoose --save
```
2.引入mongoose
```javascript
const mongoose=requie('mongoose'); 
```
3.连接本地数据库
```javascript
// 参数是mongodb:+数据库地址/数据库名称 
// useNewUrlParser 是设置新的url解析
mongoose.connect('mongodb://localhost:27017/mongo-relation', { useNewUrlParser: true });
```
3.创建集合,以创建一个users集合(即数据库表)为例
```javascript
// mongoose通过model来对应数据库中的集合 
// 第一个参数时 是集合名 第二个参数是设置集合的键以及类型
const Users=mongoose.model('Users',new mongoose.Scema({
    name:{
        type:String
    }
}));
```
4.插入数据，向users集合中插入2行文档(即数据)
```javascript
const insert=async function(){
    await Users.insertMany([{
        name:'刷新1'
    },{
        name:'shuaxin'
    }]);
    console.log('插入成功');
};
insert();
```
5.查找数据
```javascript
const find=async function(){
    // 查找集合中所有数据
    const users=await Users.find();
    cosnole.log(users);
    // 根据条件查找
    const findUser=await Users.find().where({
        name:'刷新1'
    });
    cosnole.log(findUser);
    // 限制条数查找
    const twoUsers = await Users.find().limit(2);
    console.log(twoUsers);
    // 更多使用可以看官网
};
find();
```
6.修改数据
```javascript
const updata=async function(){
    // 先查找要修改的数据
    const target=await Users.find().where({
        name:'刷新1'
    });
    target.name='刷新2';
    // 保存修改的数据
    await Users.save();
    console.log('修改成功');
};
update();
```
7.删除数据
```javascript
const del=aync function(){
    // 先查找要删除的数据
    const target=await Users.find().where({
        name:'刷新2'
    });
    // 直接删除
    await product.remove();
    console.log('删除成功');
};
del();
```
# 4.集合之间的关联
1.创建一个性别集合(Gender)和用户(Users)集合 
2.性别集合插入男(man)和女(woman)属性
3.在用户集合键类型设置时,性别类型性别集合中对应性别的id，ref为指向性别集合

```javascript
 gender:{type: mongoose.SchemaTypes.ObjectId, ref: 'Gender'}
```
4.插入数据时，先获取性别集合中对应的性别，然后设置
```javascript
// 获取性别
const man=await Gender.findOne({gender:'男'});
const woman=await Gender.findOne({gender:'女'});
// 插入数据
const res=await Users.insertMany([{
    name:'刷新',
    gender:man
},{
    name:'tao',
    gender:man
},{
    name:'guiyi',
    gender:woman
}]);
```

```javascript
    const mongoose=require('mongoose');
    mongoose.connect('mongodb://localhost:27017/userMsg', { useNewUrlParser: true });

    // 性别集合
    const Gender=mongoose.model('Gender',new mongoose.Schema({
        gender:{
            type:String
        }
    })); 
    // 用户集合
    const Users=mongoose.model('Users',new mongoose.Schema({
        name:{type:String},
        gender:{type: mongoose.SchemaTypes.ObjectId, ref: 'Gender'}
    }));
    // 向gender中插入数据
    const insertGender=async function(){
        await Gender.insertMany([{
            gender:'男'
        },{
            gender:'女'
        }]);
    };
    // 向users中插入数据
    const insertUser=async function(){
        const man=await Gender.findOne({gender:'男'});
        const woman=await Gender.findOne({gender:'女'});
        const res=await Users.insertMany([{
            name:'刷新',
            gender:man
        },{
            name:'tao',
            gender:man
        },{
            name:'guiyi',
            gender:woman
        }]);
    };
    const main=async function(){
        const gender=await Gender.find();
        const users=await Users.find();
        // 当Gender中没有数据时 执行insertGender
        if (gender.length==0) {
            await insertGender();
            console.log('插入成功');
        } else {
            // 当Gender中有数据时 直接打印
            console.log(gender);
            if(users.length==0){
                // 当Users中没有数据时 执行insertGender
                await insertUser();
                console.log('用户插入成功');
            }else{
                //  当Users中有数据时 执行insertUsers
                //  populate('gender') 显示分类属性的详细信息
                const usersDeatl=await Users.find().populate('gender');
                console.log(usersDeatl);
            };
        };
    };
    main();
```


