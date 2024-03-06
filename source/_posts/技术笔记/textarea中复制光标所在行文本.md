---
title: textarea中复制光标所在行文本
date: 2021-01-06 13:55:37
index_img: http://img.shuaxinjs.cn/thomas-tastet-hSODeSbvzE0-unsplash.jpg
banner_img: http://img.shuaxinjs.cn/thomas-tastet-hSODeSbvzE0-unsplash.jpg
category:
  - 技术笔记
tag:
  - JavaScript
  - 踩坑
---
# 前言

最近在写一个在线md编辑器，使用的textarea作为文本编辑器，来让用户进行输入；但是textarea并不具备很多日常所见到的编辑器所具备的功能，例如复制剪切光标所在行文本，甚至tab键都会带来一些意外的情况，所以记录一下这些坑。

# 相关
[textarea中tab健失效问题](https://blog.shuaxinjs.cn/2020/12/27/%E6%8A%80%E6%9C%AF%E7%AC%94%E8%AE%B0/textarea%E6%A0%87%E7%AD%BEtab%E9%94%AE%E6%97%A0%E6%95%88/)

# 需求
常见的编辑器中有个功能，是ctrl+c的时候，自动复制/剪切光标所在行的文本。

# 实现思路
1. 获取光标在文本中的位置
2. 同时向前向后查找该位置最近的两处的回车换行符位置
3. 然后截取这两个换行符之间的文本即可
- - 获取位置需要判断一下是不是最后一行，以最后是否有换行符判断即可
- - 有，就正常截取，无就按文本最后位置截取即可
- - 需要注意监听组合键
4. 将截取的字符串复制到系统的剪切板区域即可

## 获取光标位置
- 在textarea会有两种移动光标的操作：鼠标、键盘
- 监听这两个事件，然后通过触发事件的对象引用event的selectionStart、selectionEnd获取
- 键盘事件的时候需要注意，每次按键后光标移动了，但是获取到的上次按的位置，需要减一

## 获取光标位置代码
```html
<textarea id="editor" cols="30" rows="10"></textarea>
<script>
  editor.addEventListener('keydown', (e) => {
    // 光标起始位置
    let start = e.target.selectionStart
    // 光标结束位置
    let end = e.target.selectionEnd
    // 获取鼠标选中的文本
    let text=window.getSelection().toString()
  })
</script>
```


## 复制文本
- 获取到文本，如上述
- 复制到剪切区，有2中方式，如下：
- - document对象的[execCommand](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)，是一个函数，参数为对应要操作的指令字符串，如copy；通过他获取input中的选中的文本即可
- - Clipboard API的[Clipboard](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard)，因为第一个操作标准已经放弃，不建议使用；可以使用该操作代替，但是兼容性需要考虑;其次该api必须在用户交互中使用，即不能再页面加载后自动复制粘贴
- - 第三方库：[clipboardJs](https://github.com/zenorocha/clipboard.js)

## 复制文本代码
> execCommand
```html
<input type="text" id="text">
<button id="copy">copy</button>
<script>
    const copy = document.querySelector('#copy')
    const text = document.querySelector('#text')
    copy.addEventListener('click', () => {
      if (!document.execCommand('copy')) throw new Error('不支持改命令')
      // 选中文本
      text.select()
      // 执行复制 返回一个Boolean
      document.execCommand('copy')
      console.log('复制成功')
    })
</script>
```

> Clipboard
```html
<input type="text" id="text">
  <button id="copy">copy</button>
  <script>
    const copy = document.querySelector('#copy')
    const text = document.querySelector('#text')
    copy.addEventListener('click', () => {
      navigator.clipboard.writeText(text.value)
    })
</script>
```


# 复制光标所在行代码
```html
<textarea id="editor" cols="30" rows="10"></textarea>
<script>
    const editor = document.querySelector('#editor')
    // 复制文本 创建input来选中内容
    function copyText(text) {
      const input = document.createElement('input');
      document.body.appendChild(input);
      input.setAttribute('value', text);
      input.setAttribute('id', 'copy');
      input.select();
      if (document.execCommand('copy')) {
        document.execCommand('copy');
        console.log('复制成功');
      }
      document.body.removeChild(document.querySelector('#copy'))
    }
    editor.addEventListener('keydown', (e) => {
      // 是否是ctrl
      let isCtrl = e.ctrlKey
      let code = e.keyCode
      if (isCtrl && code === 67) {
        // 记录光标位置
        let start = e.target.selectionStart - 1
        let end = e.target.selectionEnd - 1
        let text = editor.value
        // 记录行首、尾
        let left = -1,
          right = -1
        // 找到第一个换行
        for (let i = start; i >= 0; i--) {
          // 0时说明为第一行
          if (text[i] === '\n' || i === 0) {
            left = i
            break
          }
        }
        // 找到第二个换行
        for (let i = end; i < text.length; i++) {
          if (text[i] === '\n' || i === text.length - 1) {
            right = i
            break
          }
        }
        // 这里主要是判断第一行、最后一行来处理换行符
        let selected = text.substring(left === 0 ? left : left + 1, right === text.length - 1 ? right + 1 : right)
        if (document.execCommand('copy')) {
          copyText(selected)
        }
      }
    })
</script>
```
