---
title: antd表格组件扩展行
category:
  - 踩坑
tag:
  - React
abbrlink: 37055
date: 2020-08-28 22:26:21
---

# 开头
- 最近入职的团队主要技术栈是react，组件库是antd；因为之前写react少，antd也没用过，所以导致上手第一时间还有蛮多不习惯的；然后就有些小坑，记录一下。

# 问题描述
- 需求是表格展示数据需要将一些数据隐藏起来，然后通过点击指定的按钮展开/收起一个面板，来显示这些数据
- 官方有提供这个功能，但是没有提供接口让你可以指定需要展开的按钮，然后查了一圈也没查到，就好好看了看文档，研究了下可以通过几个api来实现这个需求

## 步骤

>需要的api
- expandable：展开功能配置
- expandIcon：定义展开图标
- expandedRowKeys：控制展开的行

>思路说明
- 当table组件配置了expandable后，表格会自动在第一列添加个按钮（是个图标），然后通过这个按钮来控制面板的展开收起
- 1.通过定义展开图标来隐藏图标，这样就没有点击的目标了，等于取消了该开关
- 2.设置expandedRowKeys，通过表格每一行的key来控制打开或隐藏那一行面板
- 3.expandedRowKeys是一个数组，元素为字符串，就是每一行的key，当这个数组有某一行的key，这一行的面板就会被打开，没有反之；利用该特性，在我们需要指定的按钮上绑定一个事件，来动态的添加对应行的key到该数组即可

# 代码
```React
import React, { useState } from 'react'
import { Table, Button } from 'antd'
import './main.css'

const Main = () => {
    const [openKeys, setOpenKeys] = useState([])
    const handleOpenRow = (id) => {
        let index = openKeys.indexOf(id)
        if (index === -1) {
            setOpenKeys(() => {
                return [...openKeys, id]
            })
        } else {
            const arr = openKeys
            arr.splice(index, 1)
            setOpenKeys(() => {
                return [...arr]
            })
        }

    }
    const columns = [
        {
            title: 'ID',
            align: 'center',
            dataIndex: 'key',
        },
        {
            title: '姓名',
            align: 'center',
            dataIndex: 'name',
        }, {
            title: "操作",
            align: 'center',
            render: (row) => {
                return (
                    <Button type="link" onClick={() => handleOpenRow(row.key)}>
                        {
                            openKeys.indexOf(row.key) === -1 ? '展开' : '收起'
                        }
                    </Button>
                )
            }
        }
    ]
    const tableData = [{
        key: '1',
        name: "one",
        sex: "man"
    },
    {
        key: '2',
        name: 'two',
        sex: "man"
    }]
    return (
         <div className="container">
            <Table columns={columns} dataSource={tableData}
                // 开启扩展行
                expandable={{
                    // 定义扩展行内容
                    expandedRowRender: record => {
                        return <p>sex：{record.sex}</p>
                    },
                    // 自定义按钮图标
                    expandIcon: _ => '',
                    // 定义哪些行开启
                    expandedRowKeys: openKeys
                }}
            />
        </div>
    )
}
export default Main
```