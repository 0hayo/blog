---
title: 手写notice弹窗组件
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - notice弹窗组件
date: 2022-02-25 17:56:17
comments: true
tags: 
    - notice
    - 弹窗
    - 组件
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/(8).jpg.webp
---

## 手写notice弹窗组件

notice.vue

```vue
<template>
  <div v-if="isShow">
    <p>{{title}}</p>
    <p>{{text}}</p>
  </div>
</template>
<script>
export default {
  props: {
    title: {
      type: String,
      default: ''
    },
    text: {
      type: String,
      default: ''
    },
    time: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      isShow: false
    }
  },
  methods: {
    show() {
      this.isShow = true;
      this.time && setTimeout(() => {
        this.hide();
      }, this.time)
    },
    hide() {
      this.isShow = false;
      this.remove();
    }
  }
}
</script>
```

create.js

```js
import Vue from "vue";

export default function create(component, props) {
  // 创建vue实例
  const vm = new Vue({
    render(h) {
      return h(component, {props})
    }
  }).$mount();
  // 获取组件实例
  const comp = vm.$children[0];
  // 组件实例追加到body
  document.body.appendChild(vm.$el);
  // 清理函数
  comp.remove = () => {
    document.body.removeChild(vm.$el);
    vm.$destroy();
  }
  // 返回组件实例
  return comp;
}
```

使用notice组件

```js
const notice = Create(Notice, {
  title: '登录',
  text: '正确！',
  time: 3000
})
notice.show();
```
