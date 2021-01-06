---
title: textarea标签tab键无效
date: 2020-12-27 16:51:58
category:
  - 技术笔记
tag:
  - JavaScript
  - 前端
  - 踩坑
---

# 前言

最近在写一个在线md编辑器，使用的textarea作为文本编辑器，来让用户进行输入；但是textarea并不具备很多日常所见到的编辑器所具备的功能，例如复制剪切光标所在行文本，甚至tab键都会带来一些意外的情况，所以记录一下这些坑。

# 原因
- 浏览器中使用tab键会自动将光标在当前页面的各个控件上进行切换

# 解决思路
- 在textarea上监听keydown事件
- 监听每次的按键，是tab键时(code=9)，阻止默认事件
- 然后书写添加对应的空格逻辑即可

# 实现
```html
<textarea id="editor"/>
<script>
    // 阻止textarea默认事件
    const preventTab = (e) => {
        // 获取到textarea
      let dom = e.target;
      if (e.keyCode == 9) {
        //   阻止默认事件
        e.preventDefault();
        // 几个空格
        var indent = "    ";
        // 获取光标选中or光标所处字符串中的起始和截止位置
        var start = dom.selectionStart;
        var end = dom.selectionEnd;
        // 获取光标选中的内容
        var selected = window.getSelection().toString();
        selected = indent + selected.replace(/\n/g, "\n" + indent);
        dom.value =
          dom.value.substring(0, start) + selected + dom.value.substring(end);
        dom.setSelectionRange(start + indent.length, start + selected.length);
      }
    };
    let editor=document.querySelector('#editor')
    editor.addEventListener('keydown',(e)=>{
        preventTab(e)
    })
</script>
```