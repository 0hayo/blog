---
title: vite配置全局svg图标
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-04-07 10:27:17
comments: true
tags: 
    - vite
    - svg
    - 图标
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## vite.config.ts配置

```js
import path from 'path';
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons';
export default defineConfig(env => {
  return {
    plugins: [
      createSvgIconsPlugin({
        // 指定要缓存的图标文件夹
        iconDirs: [path.resolve(process.cwd(), 'src/render/assets/svg')],
        // 执行icon name的格式
        symbolId: 'icon-[dir]-[name]',
      })
    ]
  }
})
```

## SvgIcon.vue

```vue
<template>
  <svg :class="svgClass" aria-hidden="true">
    <use :xlink:href="iconName" :fill="color" />
  </svg>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue';
export default defineComponent({
  props: {
    iconClass: {
      type: String,
      required: true
    },
    className: {
      type: String,
      default: ''
    },
    color: {
      type: String,
      default: ''
    },
  },
  setup(props) {
    return {
      iconName: computed(() => `#icon-${props.iconClass}`),
      svgClass: computed(() => {
        if (props.className) {
          return `svg-icon ${props.className}`
        }
        return 'svg-icon'
      })
    }
  }
})
</script>

<style scoped>
.svg-icon {
  width: 1em;
  height: 1em;
  position: relative;
  fill: currentColor;
  vertical-align: -2px;
}
</style>
```

## main.js 引用SvgIcon.vue

```js
import { createApp } from 'vue'
import App from './App.vue'
import 'virtual:svg-icons-register';
import SvgIcon from '@/render/components/svgIcon/SvgIcon.vue';
const app = createApp(App);
app.component('svg-icon',SvgIcon);
```

## home.vue 使用SvgIcon

```vue
<template>
  <div class="home">
    <svg-icon iconClass="noNet" className="className" />
  </div>
</template>
```
