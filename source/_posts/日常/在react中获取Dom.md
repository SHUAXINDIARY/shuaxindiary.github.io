---
title: 在react中获取Dom
date: 2020-11-11 20:31:20
category:
  - 日常
tag:
  - React
  - 踩坑
---

在react中获取Dom节点
=============
用到的api：
- ref
- createRef

代码
=====
```js
// demo组件
import {createRef} from 'react'
const Demo=()=>{
    const Dom_P=createRef(null)
    const getDom=()=>{
        console.log(Dom_P)//{current:p}
        console.log(Dom_P.current)// <p>This is P</p>
    }
    return (
        <div>
            <p ref={Dom_P}>This is P</p>
            <button onClick={getDom}>Get P</button>
        </div>
    )
}
export default Demo
```