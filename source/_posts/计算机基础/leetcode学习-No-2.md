---
title: leetcode学习-No.2
category:
  - 技术笔记
tag:
  - 算法
  - javascript
abbrlink: 19194
date: 2020-10-07 21:43:15
---

# 题目
- 有效的括号
- 最长公共前缀
- 最长回文字符串

<!-- more -->

# 题解
## 有效的括号

**描述**

- 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。
- 有效字符串需满足：
  1. 左括号必须用相同类型的右括号闭合。
  2. 左括号必须以正确的顺序闭合。

**实例**

- （）true
- （）[]{} true
- (] false
- [({})] true

**思路**

- 使用栈，当遇到左括号入栈
- 遇到有括号，判断栈顶元素是不是当前右括号的左括号，是出栈，不是返回false
- 最后判断栈是否为空来判断是否匹配完成

**代码**

- ```js
  /**
   * @param {string} s
   * @return {boolean}
   */
  var isValid = function (s) {
      // 方便判断是不是匹配的括号
      const hash = {
          ')': '(',
          ']': '[',
          '}': '{'
      }
      // 判断边界
      if (s.length % 2 === 0) {
          const res = [], arr = s.split('');
          for (let i = 0; i < arr.length; i++) {
              if (arr[i] === ')' || arr[i] === '}' || arr[i] === ']') {
                  // 出栈判断是否匹配
                  let current = res.pop()
                  if (hash[arr[i]] !== current) {
                      return false
                  }
              } else {
                  // 左括号 入栈
                  res.push(arr[i])
              }
          }
          return res.length === 0 ? true : false
      }
      return false
  };
  ```



## 最长公共前缀

**描述**

- 查找字符串数组中的最长公共前缀

**示例**

- ["flower","flow","flight"]  "fl"
- ["dog","racecar","car"] ""

**思路**

- 遍历第一个元素字符串
- 每一个元素和剩下的元素挨个作对比

**代码**

- ```js
  /**
   * @param {string[]} strs
   * @return {string}
   */
  var longestCommonPrefix = function (strs) {
      // 保存结果
      let str = '';
      // 边界
      if (strs.length == 0) {
          return str
      } else if (strs.length == 1) {
          return strs[0]
      } else {
          // 正常情况
          for (let i = 0; i < strs[0].length; i++) {
              for (j = 1; j < strs.length; j++) {
                  if (strs[j][i] != strs[0][i]) {
                      return str
                  }
              }
              str += strs[0][i]
          }
          return str
      }
  };
  ```

## 最长回文子串

**描述**
- 给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

**示例**
- "babad" 输出 "bab" 或者 "aba"
- "cbbd" 输出 "bb"

**思路**
- 外层正序遍历字符串，内层倒序遍历
- 每次对比2个位置之间的字符串是否是回文即可

**代码**
```js
var longestPalindrome = function (s) {
    if (s.length === 0 || s.length === 1) {
        return s
    }
    let res = s[0], arr = s.split('')
    for (let i = 0; i < s.length; i++) {
        for (let j = s.length - 1; j > i; j--) {
            let cache = arr.slice(i, j + 1)
            let cacheStr = cache.join('')
            if (cacheStr == cache.reverse().join('')) {
                res = res.length < cacheStr.length ? cacheStr : res
            }
        }
    }
    return res
};
```
