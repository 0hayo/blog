---
title: vue异步更新机制
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - VUE
date: 2022-02-21 10:16:17
comments: true
tags: 
    - VUE
    - 异步更新机制
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/20221121143311.png
---

## vue异步更新机制

vue中更新dom是异步执行的，只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。（先缓冲，缓冲后再将多次触发属性的最后一次作为promise放入队列；而不是一上来就放队列）

vue中数据的更新到dom是异步的，数据的异步更新可以理解成一个promise的微任务；并且对同一个属性值进行多次赋值时，只有最后一次赋值会作为一个promise微任务放到更新队列。

```vue
<template>
  <div id="example">{{message}}</div>
</template>
<script>
  var vm = new Vue({
    el: '#example',
    data: {
      message: '123'
    }
  })

  Promise.resolve(1).then(value => {
    console.log("xxx", vm.$el.textContent);     // 第二步输出：123
  })

  vm.message = 'msg' // 更改数据

  Promise.resolve(2).then(value => {
    console.log("测试", vm.$el.textContent);  // 第三步输出：new
  })

  vm.message = 'new' // 更改数据

  Promise.resolve(3).then(value => {
    console.log("fff", vm.$el.textContent);         // 第四步输出：new
  })
  
  console.log(vm.$el.textContent);          // 第一步输出：123
</script>
```
