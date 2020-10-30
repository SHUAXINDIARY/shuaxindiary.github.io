---
title: leetcode学习-No.1
category:
  - 技术笔记
tag:
  - 算法
  - JavaScript
abbrlink: 19386
date: 2020-10-02 17:53:11
---

# 前言
- 随着校招和编码时间的增加觉得作为一个工程师，最最最基本的算法功底还是要有的，毕竟写轮子也好，写业务也罢，都是求最优解，说到底还是个经验+思维方式的问题，所以还是准备开始慢慢的练一练算法题，所以开个帖，记录一下刷leetcode等算法学习的笔记
- 长更帖

# 计划
- 计划按着leetcode的探索/题库先来搞，先做点简单的可以暴力解的，然后循序渐进的学习一些基本的经典算法；
- 技术笔记均为自己暴力破解的思路和看了看得懂的解题思路后，自己写出来的


<!-- more -->

# 正文

###  **寻找数组的中心索引**
>
> - 描述：数组中心索引的左侧所有元素相加的和等于右侧所有元素相加的和。有返回该索引，没有返回-1
>
> - 暴力解法思路：遍历数组，每次对当前位置的左右两边求和做比较，相等则停止，返回当前位置
>
>   - ```js
>     /**
>      * @param {number[]} nums
>      * @return {number}
>      */
>     const getSum = () => {
>         return function (nums) {
>             let i = 0, sum = 0;
>             if (nums.length > 0) {
>                 while (i < nums.length) {
>                     sum += nums[i]
>                     i++
>                 }
>             }
>             return sum
>         }
>     }
>     var pivotIndex = function (nums) {
>         let index = -1
>         let len = nums.length, start = 0
>         const res = getSum()
>         while (start < len) {
>             const left = nums.slice(0, start),
>                 right = nums.slice(start + 1, nums.length);
>             if (res(left) === res(right)) {
>                 index = start
>                 break
>             }
>             start++
>         }
>         return index
>     };
>     ```
> - 优化：
>
>   - 暴力解法每次都要重新对待验证的索引元素的两边重新遍历做和，而且都是重复运算，整个算法的时间复杂度O(n2)，有待优化
>
>   - 思路：
>
>     - 最开始以第一个元素的位置作为待验证的索引，左边初始化为0；右边为剩余元素的和；
>     - 接下来遍历数组的时候，指针每次右移动的时候对左边初始的值与当前索引的元素的前一项做和，右边对当前索引的值做差即可，时间复杂度为O(n)
>
>   - ```js
>     /**
>      * @param {number[]} nums
>      * @return {number}
>      */
>     const getSum = (nums) => {
>         let i = 0, sum = 0;
>         if (nums.length > 0) {
>             while (i < nums.length) {
>                 sum += nums[i]
>                 i++
>             }
>         }
>         return sum
>     }
>     var pivotIndex = function (nums) {
>         let index = -1
>         let len = nums.length, start = 0, leftCount = 0, rightCount = getSum(nums.slice(1, nums.length))
>         while (start < len) {
>             if (leftCount === rightCount) {
>                 index = start
>                 break
>             }
>             start++
>             leftCount += nums[start - 1]
>             rightCount -= nums[start]
>         }
>         return index
>     };
>     ```

### 搜索插入位置
>
>- 描述：在一个已排序的数组中搜索目标值，有则返回所在位置索引；灭有，就将该值插入到对应顺序的位置，然后返回给位置索引
>
>- 实例
>
>  - [1,2,3]，3  返回2
>  - [1,2]，0 返回 0 [0,1,2] 
>
>- 思路：
>
>  -  遍历数组 查找有无目标元素时，记录第一个大于目标值的元素的索引，使用min记录；当min不为-1时，说明数组没有目标值；在min位置插入目标值，然后以该位置为起点，开始倒序遍历数组，移动剩余元素即可；时间复杂度为o(n)，空间复杂度O(1)
>
>  - ```js
>    /**
>    * @param {number[]} nums
>    * @param {number} target
>    * @return {number}
>    */
>    var searchInsert = function (nums, target) {
>        let index = 0,
>            //记录第一个大于目标值的元素的索引
>            min = -1,
>            res = -1
>    	// 寻找最佳结果    
>        while (index < nums.length) {
>            if (nums[index] > target) {
>                min = index
>                break
>            }
>            if (nums[index] === target) {
>                res = index
>                break
>            }
>            index++
>        }
>        // 目标值大于数组的每一项
>        if (res === -1) {
>            nums.push(target)
>            res = nums.length - 1
>        }
>        // 插入目标元素，并后移剩余所有元素
>        if (min !== -1) {
>            let start = min
>            let len = nums.length
>            while (len > start) {
>                nums[len] = nums[len - 1]
>                len--
>            }
>            nums[min] = target
>            res = min
>        }
>    return res
>    };
>    ```
>    

### 合并区间

> - 描述：给出一个数组，数组的每一项有2个元素，第一个为左边界，第二个为有边界；合并所有的重叠区间
>
> - 实例：
>
>   - [[1,3],[2,5]]结果为[[1,5]]
>   - [[1,2],[2,5]]结果为[1,5]
>
> - 思路
>
>   - 先遍历外层数组，以内部数组的左边界为准做一个排序
>
>   - 然后从第二个元素开始遍历，判断每一项的左边界是否小于前一项的右边界以此为基准，判断两个区间是否重叠
>
>   - 当重合时，需要判断两个区间合并后的右边界是谁；这时在比较两个区间的右边界谁大就好；
>
>   - 时间复杂度为o(n)，空间复杂度为o(n)
>
>   - ```js
>     /**
>      * @param {number[][]} intervals
>      * @return {number[][]}
>      */
>     // [1,4] [2,3] [3,4]
>     var merge = function (intervals) {
>         const res = []
>         // 数组为空直接返回
>         if (intervals.length === 0) {
>             return res
>         }
>         // 根据左边界对区间排序 
>         intervals = intervals.sort((a, b) => a[0] - b[0])
>         res.push(intervals[0])
>         let index = 1
>         while (index < intervals.length) {
>             // 根据当前区间的右边界是否小于前者的左边界判断是否属于前者
>             if (intervals[index][0] <= res[res.length - 1][1]) {
>                 // 判断当前区间的右边界是否大于前者的右边界，得出合并后数组的右边界
>                 res[res.length - 1][1] = intervals[index][1] >= res[res.length - 1][1] ? intervals[index][1] : res[res.length - 1][1]
>             } else {
>                 res.push(intervals[index])
>             }
>             index++
>         }
>     
>         return res
>     };
>     ```

### 旋转矩阵

> - 描述：将二维数组构成的矩阵进行90度旋转-
>
> - 示例
>
>   - ![](http://img.shuaxinjs.cn//lc1.png)
>   - ![](http://img.shuaxinjs.cn//lc2.png)
>
> - 思路
>
>   - ![](http://img.shuaxinjs.cn//lc3.png)
>
>   - 看图发现，沿着该矩阵的对角线对称交换后，然后对每一行进行reverse即可
>
>   - 对角线交换其实就是遍历到的每一项；两个索引交换前后2个位置的值
>
>   - 不过需要注意，不能全部遍历完，不然最开始交换到的，后面又会交换回去；而且看图可以发现最后一行不会遍历到，所以外层循环只需要循环n-1次，控制层数；每一层都是从当前层数的个数开始遍历，所以内部起点为当前行数的值；
>
>   - 还有一点就是处于对角线的值，行列2个索引都是相等的，不需要进行交换，用个条件判断跳过即可
>
>   - 时间复杂度O(n)空间复杂度为O(1)
>
>   - ```js
>     /**
>      * @param {number[][]} matrix
>      * @return {void} Do not return anything, modify matrix in-place instead.
>      */
>     var rotate = function (matrix) {
>         // 标记当前项是否被交换
>         const hash = {}
>         for (let row = 0; row < matrix.length-1; row++) {
>             for (let col = row; col < matrix[row].length ; col++) {
>                 if (row !== col) {
>                     let tmp = matrix[row][col]
>                     matrix[row][col] = matrix[col][row]
>                     matrix[col][row] = tmp
>                 }
>             }
>         }
>         matrix.forEach(item => {
>             item.reverse()
>         })
>     };
>     ```

### 零矩阵

> - 描述：当一个元素为0时，将该元素所在的行和列均清为0
> - 示例：
>   
>   - ![](http://img.shuaxinjs.cn//lc4.png)
> - 思路：
>   - 用一个hash表标记一下矩阵中0出现的行和列数，然后在遍历第二遍的时候，把对应位置的数重置为0即可
>   
>   - 时间复杂度为O(n2)空间复杂度为O(n)
>   
>   - ```js
>     /**
>      * @param {number[][]} matrix
>      * @return {void} Do not return anything, modify matrix in-place instead.
>      */
>     var setZeroes = function (matrix) {
>         const hash = {
>             row: [],
>             col: []
>         }
>         matrix.forEach((row, rowIndex) => {
>             row.forEach((col, colIndex) => {
>                 if (col === 0) {
>                     hash.row.push(rowIndex)
>                     hash.col.push(colIndex)
>                 }
>             })
>         })
>         matrix.forEach((row, rowIndex) => {
>             row.forEach((col, colIndex) => {
>                 if (hash.row.indexOf(rowIndex) !== -1 || hash.col.indexOf(colIndex) !== -1) {
>                     matrix[rowIndex][colIndex] = 0
>                 }
>             })
>         })
>         return matrix
>     };
>     ```
