---
title: 前端 JS 编程题（应用类）
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
    - 前端
date: 2021-11-16 11:30:45
comments: true
tags: 
    - 前端
    - JS
    - 应用
    - 面试题
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/(5).jpg.webp
---

### 一、手写防抖（debounce）

```js
// debounce
// 举例：购物车加减数量
function debounce(fn, delay=500) {
    // timer 写在闭包中，因此防抖也是闭包的一个应用
    let timer = null;

    return function() {
        if (timer) {
            clearTimeout(timer)
        }
        timer = setTimeout(() => {
            fn.apply(this, arguments);
            timer = null;
        }, delay)
    }
}

// 验证
input1.addEventListener('keyup', debounce(() => {
  console.log(input1.value);
}), 600)
```

### 二、手写节流（throttle）

```js
// 节流
// 举例：button按钮点击后置灰
function throttle(fn, delay=100) {
  let timer = null;

  return () => {
    if (timer) return;
    timer = setTimeOut(() => {
      fn.apply(this, arguments);
      timer = null;
    }, delay)
  }
}

// 验证
div1.addEventListener(('drag', throttle(function (e) {
    console.log(e.offsetX, e.offsetY)
})))
```

### 三、事件委托

假设一个 ul 下有一万个 li，li 的 innerHTML 是从 0 到 9999，当点击某个 li 时输出该 li 代表的值

```js
window.onload = function() {
  // 创建dom
  var ul = document.getElementsByTagName("ul");
  var arr = [];
  for (let i = 0; i < 100000; i++) {
      arr.push(i);
  }
  ul[0].innerHTML = '<li>' + arr.join('</li><li>') + '</li>'

  // 事件委托 
  let ul = document.getElementById('ul');
  ul.onclick = (e) => {
    console.log(e.target.innerText)
  }
}
```

## 四、手写 Promise 加载一张图片

```js
function loadImg(scr) {
  return new Promise((resolve, reject) => {
    const img = document.createElement('img');
    img.onload = () => {
      resolve(img);
    }
    img.onerror = () => {
      reject(new Error(`图片加载失败 ${src}`))
    }
    img.src = src;
  })
}

const url = 'https://pic.leetcode-cn.com/1604237471-xbJgZl-%E5%9B%BE%E7%89%871.png';
loadImg(url).then(img => {
    console.log(img.width)
    return img
}).then(img => {
    console.log(img.height)
}).catch(ex => console.error(ex))
```

## 五、用 ajaxajax 原生实现一个 post 请求

```js
function ajax_post(url, data) {
    // 1. 异步对象 ajax
    var ajax = new XMLHttpRequest();
    
    // 2. url 方法
    ajax.open('post', url);
    
    // 3. 设置请求报文
    ajax.setRequestHeader('Content-type', 'text/plain');
    
    // 4. 发送
    if (data) {
        ajax.send(data);
    } else {
        ajax.send();
    }
    
    // 5. 注册事件
    ajax.onreadystatechange = function () {
        if (ajax.readyState === 4 && ajax.status === 200) {
            console.log(ajax.respenseText);
        }
    }
}
```

## 六、每隔一秒输出一个数字

```js
// 使用let
for (let i = 0; i < 10; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000 * i)
}

// 使用闭包
for (var i = 0; i < 10; i++) {
    (function(j) {
        setTimeout(() => {
            console.log(j);
        }, 1000 * j)
    })(i)
}

// 使用settimeout第三个参数
for(var i = 0; i <= 5; i++){           
    setTimeout((j) => {                
        console.log(j);            
    },i * 1000, i)        
}
```

## 七、 判断两个对象是否相等

```js
/*
 * @param x {Object} 对象1
 * @param y {Object} 对象2
 * @return  {Boolean} true 为相等，false 为不等
 */
 
 const deepEqual = (x, y) => {
  if (x === y) {
    return true;
  } else if ((typeof x === 'object' && x != null) && (typeof y === 'object' && y != null)) {
    if (Object.keys(x).length !=== Object.keys(y).length) return false;
    for (let prop in x) {
      if (y.hasOwnproperty(prop)) {
        if (!deepEqual(x.prop, y.prop)) return false;
      } else {
        return false;
      }
    }
    return true;
  }
  return false;
 }

//  调库实现
console.log(_.isEqual(x, y))
```
