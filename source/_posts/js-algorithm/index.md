---
title: 前端 JS 编程题（算法类）
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
    - 前端
date: 2021-11-14 14:42:45
comments: true
tags: 
    - 前端
    - JS
    - 算法
    - 面试题
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/(4).jpg.webp
---

### 一、手写快排、冒泡、选择排序

#### ① 快速排序

``` js
/**
 * --- 测试用例 ---
 *
 * 输入：[1, 34, 5, 76, 8, 6, 9, 7, 6, 3]
 * 输出：[1, 3, 5, 6, 6, 7, 8, 9, 34, 76]
 *
 * --- 说明 ---
 * 
 * 思考：快速排序是稳定的吗？
 * 解答：base 的每次选择，会导致快排是不稳定排序。
 */

const quickSort = (nums) => {
    if (nums.length < 2) {
        return nums;
    } else {
        var left = [];
        var right = [];
        var pivot = Math.floor(nums.length / 2); // Math.floor 向下取整
        var base = nums.splice(pivot, 1)[0];
        for (let i = 0; i < nums.length; i++) {
            if (nums[i] < base) {
                left.push(nums[i]);
            } else {
                right.push(nums[i]);
            }
        }
    }
    return quickSort(left).concat([base], quickSort(right));
}
```

#### ② 冒泡排序

```js
/**
 * --- 测试用例 ---
 *
 * 输入：[5, 2, 4, 7, 9, 8, 3, 6, 3, 8, 3]
 * 输出：[2, 3, 3, 3, 4, 5, 6, 7, 8, 8, 9]
 *
 * --- 说明 ---
 * 
 * 思考：冒泡排序是稳定的吗？
 * 解答：稳定。相等的元素不发生交换
 */

const bubbleSort = (nums) => {
    for (var i = 0; i < nums.length - 1; i++) {
        for (var j = 0; j < nums.length - 1 - i; j++) {
            if (nums[j] > nums[j + 1]) {
                let tmp = nums[j];
                nums[j] = nums[j + 1];
                nums[j + 1] = tmp;
            }
        }
    }
    return nums;
}
```

#### ③ 选择排序

```js
/**
 * --- 测试用例 ---
 *
 * 输入：[6, 45, 3, 2, 5, 6, 8, 4, 3, 4, 56, 67, 5]
 * 输出：[2, 3, 3, 4, 4, 5, 5, 6, 6, 8, 45, 56, 67]
 *
 * --- 说明 ---
 * 
 * 思考：选择排序是稳定的吗？
 * 解答：要看代码是如何实现的，在本例中由于有交换，所以是不稳定排序。
 */

const selectSort = (nums) => {
    var idx; // 最小值的索引
    for (var i = 0; i < nums.length - 1; i++) {
        idx = i;
        for (var j = i + 1; j < nums.length; j++) {
            if (nums[j] < nums[idx]) {
                idx = j;
            }
        }
        if (nums[i] > nums[idx]) {
            let tmp = nums[idx];
            nums[idx] = nums[i];
            nums[i] = tmp;
        }
    }
    return nums;
}s
```

## 二、URL 拆解问题

### ①

```js
/**
 * --- 题目描述 ---
 * 
 * 实现一个函数，可以对 url 中的 query 部分做拆解，返回一个 key: value 形式的 object  
 * 
 * --- 实例 ---
 * 
 * 输入：'http://sample.com/?a=1&e&b=2&c=xx&d#hash' 
 * 输出：{a: 1, b: 2, c: 'xx', d: ''}  
 */

function getQueryObj(url) {
    // TODO
    let arr = url.split('?')[1].split('#')[0].split('&');
    const res = {};
    arr.forEach(e => {
        const [key, value] = e.split('=');
        if (!value) {
            res[key] = '';
        } else {
            res[key] = value;
        }
    })
    return res;
}
```

### ②

```js
/**
 * --- 题目描述 ---
 *
 * 实现一个 parseParem 函数，将 url 转化为指定结果
 *
 * --- 测试用例 ---
 *
 * 输入：url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled'
 * 输出：
{
 user:'anonymous',
 id:[123,456],// 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型
 city:'北京',// 中文需解码
 enabled: true // 未指定值的 key 与约定为 true
}
 */

const parseParem = (url) => {
    const arr = url.split('?')[1].split('&');
    const res = {};
    arr.forEach((e) => {
        let key = e.split('=')[0], value = e.split('=')[1];
        if (value === undefined) {
            res[key] = true;
        } else {
            if (key in res) {
                Array.isArray(res[key]) ? res[key].push(value) : res[key] = [res[key]].concat(value);
            } else {
                res[key] = decodeURI(value)
            }
        }
    })
    return res;
}
```

## 三、将 HTTP header 转换成 js 对象

```JS
/**
 * --- 题目描述 ---
 *
 * 实现一个方法，把 HTTP 文本形式(字符串)的 header 转换成 JS 对象。
 * 
 * --- 测试用例 ---
 * 
 * 输入：
 * `Accept-Ranges: bytes 
 * Cache-Control: max-age=6000, public
 * Connection: keep-alive
 * Content-Type: application/javascript`
 * 输出：
 * {
 *   "Accept-Ranges": "bytes",
 *   "Cache-Control": "max-age=6000, public",
 *   Connection: "keep-alive",
 *   "Content-Type": "application/javascript"
 * }
 *
 * --- 解题思路 ---
 *
 * 1. 首先将每行数据作为数组的一个元素
 * 2. 将每个元素使用冒号分割，前面为 `key`，后面为 `value`。
 */

const solution = (s) => {
    let res = {};
    let arr = s.split("\n");
    arr.forEach((e) => {
        let tmp = e.split(": ");
        res[tmp[0]] = tmp[1];
    })
    return res;
}
```

## 四、数组扁平化

### ① 递归

```JS
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    var result = [];
    for (var i = 0, len = arr.length; i < len; i++) {
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]))
        }
        else {
            result.push(arr[i])
        }
    }
    return result;
}

console.log(flatten(arr))
```

### ② es6 拓展运算符

```JS
var arr = [1, [2, [3, 4]]];

function flatten(arr) {

    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr);
    }
    return arr;
}

console.log(flatten(arr))
```

### ③ flat

```JS
const arr1 = [0, 1, 2, [[[3, 4]]]];

console.log(arr1.flat(3)); // 3 代表数组内最多嵌套层数
// output: [0, 1, 2, 3, 4]
```
