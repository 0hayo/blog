---
title: 前端 JS 编程题（原理类）
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
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
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
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

new 一个实例会受到返回值的影响，比如：

```js
/**
 * --- Person 中 return 一个对象，p 为该对象 ---
 */

function Person(name) {
    this.name = name;
    return {age: 35}
}

Person.prototype.sayName = function() {
    console.log(this.name);
} 

const p = new Person('orange')
console.log(p) // { age: 35 }
console.log(p.name) // undefined
p.sayName(); // TypeError: p.sayName is not a function

/**
 * --- Person 返回非对象，return 不影响结果 ---
 */

function Person(name) {
    this.name = name;
    return 'free'
}

Person.prototype.sayName = function() {
    console.log(this.name);
} 

const p = new Person('orange')
console.log(p) // Person { name: 'orange' }
console.log(p.name) // orange
p.sayName(); // orange
```

### 五、手写 instanceof

```js
/*
 * --- 手动实现 instanceof ---
 */

function newInstanceOf (leftValue, rightValue) {
    if (typeof leftValue !== 'object' || rightValue == null) { 
        return false;
    }
    
    let rightProto = rightValue.prototype;
    leftValue = leftValue.__proto__;
    
    while (true) {
        if (leftValue === null) return false;
        if (leftValue === rightProto) return true;
        leftValue = leftValue.__proto__;
    }
}

/*
 * --- 验证 ---
 */

const a = [];
const b = {};

function Foo () {}

var c = new Foo()
function Child () {}
function Father() {}
Child.prototype = new Father()
var d = new Child()

console.log(newInstanceOf(a, Array)) // true
console.log(newInstanceOf(b, Object)) // true
console.log(newInstanceOf(b, Array)) // false
console.log(newInstanceOf(a, Object)) // true
console.log(newInstanceOf(c, Foo)) // true
console.log(newInstanceOf(d, Child)) // true
console.log(newInstanceOf(d, Father)) // true
console.log(newInstanceOf(123, Object)) // false 
console.log(123 instanceof Object) // false
```

这个问题既考察了 instanceof 的原理，又考察了原型链，还考察了代码能力，是一个好问题。在实现代码中，我们判断 leftValue 是否为 rightValue 的实例，思想是在 leftValue 的原型链上，即 leftValue.__proto__ 上寻找是否存在 rightValue.prototype。

### 六、实现 Promise.all()

1、接收一个 Promise 实例的数组或具有 Iterator 接口的对象，

2、如果元素不是 Promise 对象，则使用 Promise.resolve 转成 Promise 对象

3、如果全部成功，状态变为 resolved，返回值将组成一个数组传给回调

4、只要有一个失败，状态就变为 rejected，返回值将直接传递给回调all() 的返回值也是新的 Promise 对象

```js
function promiseAll(promises) {
  return new Promise(function(resolve, reject) {
    if (!isArray(promises)) {
      return reject(new TypeError('arguments must be an array'));
    }
    var resolvedCounter = 0;
    var promiseNum = promises.length;
    var resolvedValues = new Array(promiseNum);
    for (var i = 0; i < promiseNum; i++) {
        (function(i) {
            Promise.resolve(promises[i]).then(function(value) {
            resolvedCounter++
            resolvedValues[i] = value
            if (resolvedCounter == promiseNum) {
                return resolve(resolvedValues)
            }
            }, function(reason) {
            return reject(reason)
            })
        })(i)
    }
  })
}
```
