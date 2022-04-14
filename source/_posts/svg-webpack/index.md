---
title: webpack配置全局svg图标
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-03-17 15:19:17
comments: true
tags: 
    - webpack
    - svg
    - 图标
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## webpack配置

```js
const { defineConfig } = require('@vue/cli-service')
const path = require('path')
function resolve(dir) {
  return path.join(__dirname, dir)
}
const port = 7070;
const title = 'webpack测试';
module.exports = defineConfig({
  transpileDependencies: true,
  lintOnSave: false, // 是否在开发环境下每次保存代码时都启用 eslint验证
  devServer: {
    port: port
  },
  chainWebpack(config) {
    config.module.rule('svg')
      .exclude.add(resolve('src/assets'))

    // 添加svg-sprite-loader
    config.module.rule('svg-icon')
      .test(/\.svg$/)
      .include.add(resolve('src/assets'))
        .end()
      .use('svg-sprite-loader') //  添加loader
        .loader('svg-sprite-loader')//  切换上下文到loader
        .options({symbolId: 'icon-[name]'})
        .end()
  }
})
```

## icon.vue

```vue
<template>
  <div class="icon">
    <svg>
      <use :xlink:href="iconName" :class="svgClass"></use>
    </svg>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';
@Component({
  components: {
  },
})
export default class HomeView extends Vue {
  @Prop() private iconClass!: string;
  @Prop() private className!: string;

  get iconName(): string {
    return `#icon-${this.iconClass}`;
  }

  get svgClass(): string {
    if(this.className) {
      return `icon-svg ${this.className}`;
    }
    return 'icon-svg';
  }
}
</script>
<style scoped>
.icon-svg {
  
}
</style>
```

## icon.js

```js
import Vue from "vue";
import Icon from '@/components/icon.vue'
// 图标自动导入
// 利用webpack 的require.context自动导入

// 返回的req是只去加载svg目录中的模块的函数
const req = require.context('./svg', false, /\.svg$/) //  路径， 是否递归， 匹配规则

req.keys().map(req);

Vue.component('Icon', Icon)
```

## main.js 引用icon.js

```js
import '@/assets/index'
```

## home.vue 使用icon

```vue
<template>
  <div class="home">
    <icon icon-class="wx"></icon>
    <icon icon-class="qq"></icon>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component({
  components: {}
})
export default class HomeView extends Vue {}
</script>

```
