---
title: antd menu组件
date: 2020-11-11 21:22:08
category:
  - 技术笔记
tag:
  - React
  - 踩坑
---

antd menu组件
====================
继续记录antd+react使用


场景
===
1.每次刷新浏览器后，menu应该继续选中当前url对应的menuitem
2.限制只能打开一个menugroup

<!-- more -->

## 代码&思路
每次刷新浏览器后，menu应该继续选中当前url对应的menuitem
- 场景1思路：利用react路由的withRouter包裹当前组件，然后组件内部通过props就可以得到当前路由对象location；location的pathname就是当前的url，然后将该值赋给defaultSelectedKeys即可
- 场景2思路：menu组件打开的menu是通过openKeys控制，每次点击要打开的menu的时候，去将openkeys绑定的数组重置为当前点击的菜单的key即可
```js
// Nav组件
import {  withRouter } from 'react-router-dom';
const Nav=({location})=>{
    const [keys, setKeys] = useState(['sub1'])
    const saveKeys = ({ key }) => {
    setKeys(() => [key])
    }
    return (
    <Menu
      onClick={handleClick}
      defaultSelectedKeys={[location.pathname]}
      openKeys={keys}
      mode="inline"
      style={{ width: "100 %", height: "100%" }}
    >
      <SubMenu
        onTitleClick={saveKeys}
        key="sub0"
        title={
          <span>
            <CopyOutlined />
            <span>概览</span>
          </span>
        }
      >
        <Menu.Item key="/">
          <Link to="/">首页</Link>
        </Menu.Item>
      </SubMenu>
      <SubMenu
        onTitleClick={saveKeys}
        key="sub0"
        title={
          <span>
            <CopyOutlined />
            <span>概览</span>
          </span>
        }
      >
        <Menu.Item key="/about">
          <Link to="/about">关于</Link>
        </Menu.Item>
      </SubMenu>
    </Menu>
    )
}
export default withRouter(Nav)
```