---
title: 关于JWT认证
category:
  - 笔记
tags:
  - 踩坑
  - NodeJS
abbrlink: 53894
date: 2020-03-15 22:57:35
---

# 开头
- 但凡有登陆模块的程序，都会涉及到用户身份认证的问题;目的就是确保来访问是真的用户
- 所以记录一下最近这一块学习/实验的笔记
- 实现代码均为部分，整体代码[点这里](https://github.com/SHUAXINDIARY/NodeStudyNotes/tree/master/loginByExpress)

# 工作流程
- 用户登陆，如果登陆信息和服务端保存的信息一致，服务端会通过一些机制生成一串字符串，返回给客户端
- 客户端保存该字符串，并在之后的每次请求中携带该字符串
- 服务端接收到请求后，会验证该字符串是否有效、是否过期;如果都没有就正常返回数据，如果失效、过期就返回对应的提示信息
- 这串字符串就是token令牌

# 小插曲
- 做登陆等需要处理用户账号密码等私密的操作时，要对这些隐私信息进行加密，在做操作、保存等 
- 目的：1.职业操守，开发人员要尊重用户的这些私密信息；2.防止被其他人攻击，拿到密码等私密信息进行一些不好的操作

# 加密的代码实现
- 需要用到[bcrypt](https://github.com/kelektiv/node.bcrypt.js)模块
- 对注册密码进行加密并保存，方便演示，数据直接保存到本地数组
```js
...
const express = require('express')
const router = express.Router()
const bcrypt = require('bcrypt')

// 保存注册信息
let users=[]

// 生成salt的等级  
const saltRounds = 10
// 注册
router.post('/register', (req, res, next) => {
    /**
        body={
            name:'shuaxin',
            psw:'shuaxin'
        }
    */
    // 接受前端数据
    let obj = req.body
    // 生成salt
    const salt = bcrypt.genSaltSync(saltRounds)
    // 利用salt对密码进行hash生成加密字符串
    const hash = bcrypt.hashSync(obj.psw, salt)
    // 然后保存该条数据等操作
    users.push({
        name:obj.name,
        psw:hash
    })
    // 返回提示
    res.json({
        msg: 'register succ'
    })
})
// 登陆
router.post('/login', (req, res, next) => {
    let obj = req.body
    // 查找对应的账号 然后校对密码是否一致
    for (let index in users) {
        if (users[index].name === obj.name) {
            // 校对结果为Boolean
            // 参数列表(用户输入的面，存储的加密后的密码)
            let result = bcrypt.compareSync(obj.psw, users[index].psw)
            if (result) {
                res.json({
                    code: 1,
                    msg: '登陆成功',
                    token
                })
            } else {
                res.json({
                    code: -1,
                    msg: '检查账号密码是否匹配'
                })
            }
            break
        }
    }
})
...
```

# 生成token代码实现
- 用到的模块[jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
- 登陆成功返回生成的token
- 在上文代码的登陆逻辑区域操作，省略重复代码
```js
...
// 引入模块
const jwt = require('jsonwebtoken')
// 设置私钥
const sk = 'shuaxin'
...
// 登陆
router.post('/login', (req, res, next) => {
    let obj = req.body
    for (let index in users) {
        if (users[index].name === obj.name) {
            let result = bcrypt.compareSync(obj.psw, users[index].psw)
            // 生成token  参数:(要加密的内容，秘钥，时间(s为单位))
            let token = jwt.sign(users[index], sk, { expiresIn: 10 });
            if (result) {
                res.json({
                    code: 1,
                    msg: '登陆成功',
                    token
                })
            } else {
                res.json({
                    code: -1,
                    msg: '检查账号密码是否匹配'
                })
            }
            break
        }
    }
})
...
```
- 验证token是否有效、过期
```js
...
// 引入模块
const jwt = require('jsonwebtoken')
// 设置私钥
const sk = 'shuaxin'
...
router.post('/testToken', (req, res, next) => {
    let obj = req.body
    // 过期后会抛异常 
    try {
        // 校验token 结果为Boolean
        // 参数列表（token,私钥）
        var decoded = jwt.verify(obj.token, sk);
        if (decoded) {
            res.json({
                code: 1,
                msg: '有效的token'
            })
        } else {
            res.json({
                code: -1,
                msg: 'token无效'
            })
        }
    } catch (error) {
        res.json({
            code: -1,
            msg: 'token过期'
        })
    }
})
```

# 其他笔记
- 看jwt文档的时候看到了HMAC SHA256、RSA SHA256
- 这两个是不同的加密算法模式，前者是默认的，后者可以自己设置
- 通过搜索引擎了解到常见加密算法模式有：DES,AES, RSA, MD5, SHA, HMAC, base64，区别有待学习
- SHA256是一种使用了哈希长度为256的算法，太菜了，底层就不懂了
- [DES,AES, RSA, MD5, SHA, HMAC, base64区别](https://www.jianshu.com/p/567071aadc42)

# 关于JWT的优缺点
优缺点都是对比出来的，和常用的session进行对比，由于实践有限，session没用过，总结是搜索引擎搜索后，罗列的自己能理解的

>JWT优点
- 减轻服务器压力，因为生成后直接返回给客户端，服务端就无须保存，session需要维持每个用户的状态
- 避免跨域攻击，因为服务端会对每个请求进行token校验，不存在或者无效都没法获取数据，而token保存在用户自己的客户端，只要不在这里被窃取，来自网络的伪造请求一般都无法完成
- 适合移动端，没做过移动端，查了查资料，因为session会话状态的控制需要依赖cookie，客户端对cookie的支持很有限，所以session不适合移动端
- 实现单点登录，因为cookie不能跨域，session依赖cookie，token不存在这个问题

>JWT缺点
- 注销登录、忘记密码等后，token还有效，这样服务端需要加一些额外操作来处理
- token有效期续签问题，续签的目的就是为了防止用户频繁需要登录，token也需要额外的一些解决方案

>参考文章
[JWT优缺点](https://zhuanlan.zhihu.com/p/85873228)