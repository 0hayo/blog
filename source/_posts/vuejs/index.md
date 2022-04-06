---
title: 简单实现vue双向绑定以及v-text、v-model、v-html、@click、插值表达式
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-03-11 16:30:17
comments: true
tags: 
    - 原理
    - vue
    - 双向绑定
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## html页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Vue</title>
</head>
<body>
  <script src="Untitled-1.js"></script>
  <script src="Compile.js"></script>
  <div id="app">
    <p>{{name}}</p>
    <p v-text="name"></p>
    <p>{{age}}</p>
    <input type="text" v-model="model">
    <div v-html="html"></div>
  </div>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        name: 'jina',
        // obj: {
        //   age: 18
        // },
        age: 12,
        model: 'aaa',
        html: '<button @click="click">这是一个按钮</button>'
      },
      created() {
        // setTimeout(() => {
        //   this.name = '修改测试'
        // }, 2000);
      },
      metheds: {
        click() {
          this.name = 'jierui';
          console.log(this.name);
        }
      }
    })
    // app.name = 'zong';
    // app.obj.age = 12;
    // console.log(app.$data);
  </script>
</body>
</html>
```

## 实现Vue构造函数

```js
class Vue {
  constructor(options) {
    // 保存数据
    this.$options = options;
    // 传入data
    this.$data = options.data;
    this.$metheds = options.metheds;
    // 响应化处理
    this.observe(this.$data);

    // Watcher 测试
    // new Watcher(this, 'name');
    // this.name;
    // new Watcher(this, 'obj.age');
    // this.obj.age;

    // 编译器测试
    new Compile(options.el, this);

    if (options.created) {
      options.created.call(this);
    }
  }

  observe(data) {
    // 暂不考虑数组和函数
    if (!data || typeof data !== 'object') return;
    Object.keys(data).forEach(key => {
      this.defineReactive(data, key, data[key]);
      // 代理data中的属性到vue根上
      this.proxyData(key);
    })
  }

  defineReactive(data, key, value) {
    // 递归处理
    this.observe(value);
    // 定义一个Dep
    const dep = new Dep();
    // 给data中的每个key定义拦截
    Object.defineProperty(data, key, {
      get() {
        // 收集依赖
        Dep.target && dep.addDep(Dep.target);
        return value;
      },
      set(newValue) {
        if (newValue !== value) {
          value = newValue;
          console.log(key + ' 数据更新！')
          dep.notify();
        }
      }
    })
  }

  proxyData(key) {
    Object.defineProperty(this, key, {
      get() {
        return this.$data[key];
      },
      set(newValue) {
        this.$data[key] = newValue;
      }
    })
  }
}

// 创建dep 管理所有watcher
class Dep {
  constructor() {
    // 存储所有依赖
    this.watchers = [];
  }

  addDep(watcher) {
    this.watchers.push(watcher);
  }

  notify() {
    this.watchers.forEach(watcher => watcher.update())
  }
}

// 创建watcher 保存data中数值和页面中的挂钩关系
class Watcher {
  constructor(vm, key, cb) {
    this.vm= vm;
    this.key = key;
    this.cb = cb;
    // 创建实例时 立刻将该实例指向静态属性Dep.target便于依赖收集
    Dep.target = this;
    this.vm[key];
    Dep.target = null;
  }

  update() {
    console.log(this.key + '更新了！');
    this.cb.call(this.vm, this.vm[this.key]);
  }
}
```

## 实现Compile构造函数

```js
// 遍历dom结构，解析指令和插值表达式
class Compile {
  // el: 待编译模板  vm: Vue实例
  constructor(el, vm) {
    this.$el = document.querySelector(el);
    this.$vm = vm;

    // 把模板中的内容移到片段操作
    this.$fragment = this.nodeFragment(this.$el);
    // 执行编译器
    this.compile(this.$fragment);
    // 放回$el中
    this.$el.appendChild(this.$fragment);
  }

  nodeFragment(el) {
    /*
      DocumentFragments 是DOM节点。它们不是主DOM树的一部分。
      通常的用例是创建文档片段，将元素附加到文档片段，然后将文档片段附加到DOM树。
      在DOM树中，文档片段被其所有的子元素所代替。
      因为文档片段存在于内存中，并不在DOM树中，所以将子元素插入到文档片段时不会引起页面回流（对元素位置和几何上的计算）。
      因此，使用文档片段通常会带来更好的 性能 
    */
   const fragment = document.createDocumentFragment();
   let child;
   while (child = el.firstChild) {
     fragment.appendChild(child);
   }
   return fragment;
  }

  compile(el) {
    const childNodes = el.childNodes;
    Array.from(childNodes).forEach(node => {
      if (node.nodeType === 1) {
        // 元素
        console.log('编译元素' + node.nodeName);
        this.compileElement(node);
      } else if (this.isInter(node)) {
        // 文本
        console.log('编译插值文本' + node.textContent);
        this.compileText(node);
      }
      // 递归子节点
      if (node.children && node.childNodes.length > 0) {
        this.compile(node);
      }
    })
  }

  // 正则表达式解析是否为文本
  isInter(node) {
    return node.nodeType === 3 && /\{\{(.*)\}\}/.test(node.textContent)
  }

  // 文本编译
  compileText(node) {
    const exp = RegExp.$1;
    this.update(node, exp, 'text');
    node.textContent = this.$vm[RegExp.$1];
  }

  // 元素编译
  compileElement(node) {
    const nodeAttrs = node.attributes;
    Array.from(nodeAttrs).forEach(attr => {
      // v-xxx="yyy"
      const attrName = attr.name;// v-xxx
      const exp = attr.value;// yyy
      if (attrName.indexOf('v-') === 0) {
        const dir = attrName.substring(2);
        this[dir] && this[dir](node, exp, dir);
      } else if (attrName === '@click') {
        const _this = this;
        node.addEventListener('click', function() {
          _this.$vm.$metheds[exp].call(_this.$vm);
        })
      }
    })
  }

  // 更新
  update(node, exp, dir) {
    const updator = this[dir + 'Updator'];
    updator && updator(node, this.$vm[exp]);
    new Watcher(this.$vm, exp, function(value) {
      updator && updator(node, value);
    });
  }

  // v-text 更新处理
  text(node, exp, dir) {
    this.update(node, exp, dir)
  }
  // 修改文本的值
  textUpdator(node, value) {
    node.textContent = value;
  }

  // v-html
  html(node, exp, dir) {
    this.update(node, exp, dir)
  }
  htmlUpdator(node, value) {
    node.innerHTML = value;
  }

  // v-model
  model(node, exp, dir) {
    this.update(node, exp, dir);
    const _this = this;
    node.addEventListener('input', function(InputEvent) {
      _this.$vm[exp] = InputEvent.target.value;
    })
  }
  modelUpdator(node, value) {
    node.value = value;
  }

}
```
