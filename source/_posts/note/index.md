---
title: 笔记
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-02-23 14:58:17
comments: true
tags: 
    - 笔记
    - vue
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/(7).jpg.webp
---

## 组件通信

### 父 => 子

 props

```js
// child
props: { msg: String }

// parent
<helloWorld msg="welcome" />
```

 $refs

```js
// parent
<helloWorld msg="welcome" ref="helloWorld" />
this.$refs.helloWorld.xx = 'xxx'
```

 $children

```js
// parent $children不保证子组件顺序
this.$children[0].xx = 'xxx'
```

### 子 => 父

 $emit

```js
// child
this.$emit('add', 'msg')

// parent
<helloWorld @add="add" />
```

### 兄弟组件通信

 $parent / $root

```js
// brother1 监听
this.$parent.$on('foo', 'msg')
// brother1 派发
this.$parent.$emit('foo')
```

### 祖先后代组件通信

注：官方不建议项目使用，并且不建议修改

```js
// 祖
provide() {
  return {
    foo: 'foo'
  }
}

// 后
inject: ['foo']
```

### 任意两个组件通信

```js
Vue.prototype.$bus = new Vue()

// child1
this.$bus.$on('foo', handle)
// child2
this.$bus.$emit('foo', 'msg')
```

## 插槽

```js
// 匿名插槽
<div>
  <slot></slot>
</div>
<helloWorld>
  <template v-slot:default></template>
</helloWorld>

// 具名插槽
<div>
  <slot name="main"></slot>
</div>
<helloWorld>
  <template v-slot:main></template>
</helloWorld>

// 作用域插槽
<div>
  <slot name="main" msg="msg"></slot>
</div>
<helloWorld>
  <template v-slot:main={msg}>{{ msg }}</template>
</helloWorld>
```
