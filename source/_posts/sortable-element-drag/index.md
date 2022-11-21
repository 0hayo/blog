---
title: sortable+element实现表格行拖拽
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
    - 拖拽
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/20221121143322.jpg
---

### 安装

```bash
npm install vuedraggable
```

### 引入

```js
import Sortable from 'sortablejs'
```

### el-table 添加row-key属性

```html
<el-table
  ref="filterTable"
  row-key="ip" 
  @filter-change="handlerFilterChange" 
  class="cl-table" 
  :data="resourceList" 
  v-loading="resourceListLoading" 
  stripe
  style="width:100%;">
  <el-table-column
    prop="name"
    label="主机名" 
    :min-width="150" 
    show-overflow-tooltip>
  </el-table-column>
</el-table>
```

### 将拖拽元素设置为要拖动项的父级元素

```js
mounted() {
  // 表格中需要实现行拖动，所以选中tr的父级元素
  const table = document.querySelector('.el-table__body-wrapper tbody')
  const self = this
  Sortable.create(table, {
    onEnd({ newIndex, oldIndex }) {
      console.log(newIndex, oldIndex)
      const targetRow = self.resourceList.splice(oldIndex, 1)[0]
      self.resourceList.splice(newIndex, 0, targetRow)
    }
  })
}
```
