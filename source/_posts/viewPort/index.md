---
title: viewPort适配移动端
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - VUE
    - 移动端
date: 2020-04-07 16:42:17
comments: true
tags: 
    - VUE
    - 移动端
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/20221121143259.png
---

### PostCSS 基本插件

默认安装vue-cli 脚手架

```js
module.exports = {
   "plugins": {
   "postcss-import": {},
        "postcss-url": {},
        // to edit target browsers: use "browserslist" field in package.json
        "autoprefixer": {}
    }
}
```

### PostCSS 其他插件

安装其他插件

```bash
npm i postcss-aspect-ratio-mini postcss-px-to-viewport postcss-write-svg postcss-cssnext postcss-viewport-units cssnano --S
```

安装成功之后 package.json 显示：

```js
"dependencies": {
    "axios": "^0.18.0",
    "cssnano": "^4.1.0",
    "postcss-aspect-ratio-mini": "0.0.2",
    "postcss-cssnext": "^3.1.0",
    "postcss-px-to-viewport": "0.0.3",
    "postcss-viewport-units": "^0.1.4",
    "postcss-write-svg": "^3.0.1",
    "vue": "^2.5.2",
    "vue-router": "^3.0.1",
    "vuex": "^3.0.1" 
}
```

### .postcssrc.js 文件设置

脚手架原有的三个配置项需要注释 autoprefixer ，理由是 cssnano 中已经具有此配置

```js
module.exports = {
    "plugins": {
        "postcss-import": {},
        "postcss-url": {},
        // to edit target browsers: use "browserslist" field in package.json
        // "autoprefixer": {}, 
        "postcss-aspect-ratio-mini": {},
        "postcss-write-svg": {
            utf8: false
        },
        "postcss-cssnext": {},
        "postcss-px-to-viewport": {
            viewportWidth: 750, // 视窗的宽度，对应的是我们设计稿的宽度，一般是750
            // viewportHeight: 1334, // 视窗的高度，根据750设备的宽度来指定，一般指定1334，也可以不设置
            unitPrecision: 3, // 指定`px`转换为视窗单位值的小数位数（很多时候无法整除）
            viewportUnit: 'vw', // 指定需要转换成的视窗单位，建议使用vw
            selectorBlackList: ['.ignore', '.hairlines'], // 指定不转换为视窗单位的类，可以自定义， 可以无限添加,建议定义一至两个通用的类名 
            minPixelValue: 1, // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值
            mediaQuery: false // 允许在媒体查询中转换`px`
        },
        "postcss-viewport-units": {},
        "cssnano": {
            preset: "advanced",
            autoprefixer: false,
            "postcss-zindex": false
        }
    }
}
```

### cssnano 插件安装

在配置中我们使用了 preset: “advanced” ，所以还需要安装

```bash
npm i cssnano-preset-advanced --save-dev
```

基本配置

```js
"cssnano": {
    preset: "advanced",
    autoprefixer: false,
    "postcss-zindex": false
}
```

### postcss-px-to-viewport

这是核心的插件，帮助我们将 px 转成 vw、vh、vmin、vmax
目前出视觉设计稿，都是使用 750px，那么 100vw = 750px，即 1vw = 7.5px。那么我们根据设计图上 的px 值直接转换成对于的 vw 值。实际撸码过程中，不需要做任何计算，直接在代码中写 px 即可
