---
title: 前端 JS 编程题（原理类）
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: hojun.cn
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
    - 前端
date: 2021-11-14 16:42:45
comments: true
tags: 
    - 前端
    - JS
    - 原理
    - 面试题
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://static.2heng.xin/wp-content/uploads//2019/02/wallhaven-672007-1-1024x576.png
---

### 一、 深拷贝

```js
// deepClone
function deepClone(obj = {}) {
    if (typeof obj !== 'object' || obj == null) {
        // obj 是 null 或者不是对象和数组，直接返回
        return obj;
    }
    let res;
    if (obj instanceof Array) {
        res = [];
    } else {
        res = {};
    }

    for (let key in obj) {
        // 判断自身中是否包含自身属性
        if (obj.hasOwnProperty(key)) {
            res[key] = deepClone(obj[key])
        }
    }
    return res;
}
// 验证
o = {a: 1, d: {c: '4'}};
res = deepClone(o);
console.log(res);
console.log(res == o);
```

### 二、浅拷贝

```js
// 首先定义一个对象
const hero = {
  name: 'Batman',
  city: 'Gotham'
};
// **********************方法一**********************
const heroEnhancedClone = {
  ...hero,
  name: 'Batman Clone',
  realName: 'Bruce Wayne'
};

// 验证
// { name: 'Batman Clone', city: 'Gotham', realName: 'Bruce Wayne' }
heroEnhancedClone;

// **********************方法二**********************
const { ...heroClone } = hero;

// 验证
heroClone; // { name: 'Batman', city: 'Gotham' }
hero === heroClone; // => false

// **********************方法三**********************
const hero = {
  name: 'Batman',
  city: 'Gotham'
};

// 验证
const heroClone = Object.assign({}, hero);
heroClone; // { name: 'Batman', city: 'Gotham' }
hero === heroClone; // => false
```

### 三、手写 bind 函数

```js
// 模拟 bind
Function.prototype.bind1 = function() {
    // 将参数拆解为数组
    const args = Array.prototype.slice.call(arguments) // 变成数组
    
    // 获取 this（数组第一项）
    const t = args.shift()
    
    // fn1.bind(...) 中的 fn1
    const self = this
    
    // 返回一个函数
    return function() {
        return self.apply(t, args)
    }
}
function fn1(a, b, c){
    console.log('this', this)
    console.log(a, b, c)
    return 'this is fn1'
}
const fn2 = fn1.bind1({x: 100}, 10, 20, 30)
const res = fn2()
console.log(res)
```

### 四、手写 new

```js
function funcNew(obj, ...args) {
    const newObj = Object.create(obj.prototype);
    const result = obj.apply(newObj, args);
    return (typeof result === 'object' && result !== null) ? result : newObj;
}
```
