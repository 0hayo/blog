---
title: vue-element-table-滚动加载
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - VUE
    - element
date: 2020-04-07 16:42:45
comments: true
tags: 
    - VUE
    - element
    - table
    - 滚动加载
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

首先在main.js文件注册全局事件，代码如下：

```js
Vue.directive('loadmore', {
  bind(el, binding) {
    var p = 0;
    var t = 0;
    var down = true;
    var selectWrap = el.querySelector('.el-table__body-wrapper')
    selectWrap.addEventListener('scroll', function() {
      //判断是否向下滚动
      p = this.scrollTop;
      // if ( t < p){down=true}else{down=false}
      if(t < p){
        down = true;
      }else{
        down = false;
      }
      t = p;
      //判断是否到底
      const sign = 10;
      const scrollDistance = this.scrollHeight - this.scrollTop - this.clientHeight
      if (scrollDistance <= sign && down) {
        binding.value()
      }
    })
  }
})
```

在组件中，使用自定义的事件：
v-loadmore=”loadMore”
在methods中调用loadMore
这样就实现啦！！！
