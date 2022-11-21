---
title: 手写form组件
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - form
date: 2022-02-25 14:04:17
comments: true
tags: 
    - form
    - 组件
    - 表单验证
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/(1).jpg.webp
---

## form组件

input组件

```vue
<template>
  <div>
    <input :value="value" @input="onInput" v-bind="$attrs">
  </div>
</template>
<script>
export default {
  inheritAttrs: false,
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  methods: {
    onInput(e) {
      this.$emit('input', e.target.value);
      // 防止input多层嵌套，formItem无法正常派发事件
      const parent = this.getParent(this.$parent);
      parent.$emit('validate');
    },
    getParent(parent) {
      if (parent.$options._componentTag === 'CFormItem') return parent;
      this.getParent(parent.$parent);
    }
  }
}
</script>
```

checkbox组件

```vue
<template>
  <div>
    <input type="checkbox" :checked="checked" @change="onCahnge" v-bind="$attrs">
  </div>
</template>
<script>
export default {
  inheritAttrs: false,
  props: {
    checked: {
      type: Boolean,
      default: false
    }
  },
  model: {
    prop: 'checked',
    event: 'change'
  },
  methods: {
    onCahnge(e) {
      this.$emit('change', e.target.checked);
      const parent = this.getParent(this.$parent);
      parent.$emit('validate');
    },
    getParent(parent) {
      if (parent.$options._componentTag === 'CFormItem') return parent;
      this.getParent(parent.$parent);
    }
  }
}
</script>
```

formItem组件

```vue
<template>
  <div>
    <label v-if="label">
      {{ label }}
    </label>
    <slot></slot>
    <p v-if="erroeMsg">{{ erroeMsg }}</p>
  </div>
</template>
<script>
/*
  表单异步验证js库：async-validator，element-ui中的form组件所使用的验证方式。
  npm install --save async-validator
*/
import Schema from 'async-validator';
export default {
  props: {
    label: {
      type: String,
      default: ''
    },
    prop: {
      type: String
    }
  },
  inject: ['form'],
  data() {
    return {
      erroeMsg: ''
    }
  },
  mounted() {
    this.$on('validate', () => {
      this.validate();
    })
  },
  methods: {
    validate() {
      // 获取校验规则
      const rule = this.form.rules[this.prop];
      // 获取数值
      const value = this.form.model[this.prop];
      // 执行校验
      const desc = {
        [this.prop]: rule
      };
      const schema = new Schema(desc);
      return schema.validate({[this.prop]: value}, errors => {
        if (errors) {
          this.erroeMsg = errors[0].message;
        } else {
          this.erroeMsg = '';
        }
      });
    }
  }
}
</script>
```

form组件

```vue
<template>
  <div>
    <slot></slot>
  </div>
</template>
<script>
export default {
  props: {
    model: {
      type: Object,
      requireg: true
    },
    rules: {
      type: Object
    }
  },
  provide() {
    return {
      form : this
    }
  },
  data() {
    return {
    }
  },
  methods: {
    validate(cb) {
      const tasks = this.$children
        .filter(item => item.prop)
        .map(item => item.validate());
      Promise.all(tasks)
        .then(() => cb(true))
        .catch(() => cb(false))
    }
  }
}
</script>
```

引用form组件

```vue
<template>
  <div class="wrap">
    <CForm :model="model" :rules="rules" ref="form">
      <CFormItem label="用户名" prop="userName">
        <CInput v-model="model.userName"></CInput>
      </CFormItem>
      <CFormItem label="密码" prop="passWord">
        <CInput v-model="model.passWord" type="passWord"></CInput>
      </CFormItem>
      <CFormItem label="勾选" prop="checked">
        <CCheckBox v-model="model.checked"></CCheckBox>
      </CFormItem>
      <CFormItem>
        <button @click="login">登录</button>
      </CFormItem>

      {{ model }}
    </CForm>
  </div>
</template>
<script>
import CForm from '@/components/form/index';
import CInput from '@/components/form/input.vue'
import CCheckBox from '@/components/form/checkBox'
import CFormItem from '@/components/form/formItem.vue'
export default {
  components: {
    CForm,
    CInput,
    CFormItem,
    CCheckBox
  },
  data() {
    return {
      model: {
        userName: '',
        passWord: '',
        checked: false
      },
      rules: {
        userName: [{required: true, message: "请输入用户名"}],
        passWord: [{required: true, message: "请输入密码"}],
        checked: [{validator: (rule, value) => value, message: "请勾选"}]
      }
    }
  },
  methods: {
    login() {
      this.$refs.form.validate((isValid) => {
        if (isValid) {
          alert('登录！')
        } else {
          alert('错误！')
        }
      })
    }
  },
  created() {
    // console.log('路由查询参数',this.$tab.query())
  },
}
</script>
<style scoped>
</style>
```
