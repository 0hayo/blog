---
title: 数组遍历的实现
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - JS
date: 2022-02-23 10:18:17
comments: true
tags: 
    - JS
    - Array
    - 遍历
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## 实现数组的filter方法

```js
Array.prototype.filter => (fn) {
  let arr = [];
  for (let i = 0; i < this.lenght; i++) {
    if (fn(this[i], i, this)) {
      arr.push(this[i])
    }
  }
  return arr;
}
```
