---
title: vue-property-decorator用法
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-03-17 15:33:17
comments: true
tags: 
    - vue
    - vue-property-decorator
    - ts
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: /images/cover/20221121143259.png
---

### vue-property-decorator

#### data

```vue
<script lang="ts">
    import {Vue, Component} from 'vue-property-decorator';

    @Component({})
    export default class "组件名" extends Vue{
        ValA: string = "hello world";
        ValB: number = 1;
    }
</script>
```

#### computed

```vue
<script lang="ts">
    import {Vue, Component} from 'vue-property-decorator';

    @Component({})
    export default class "组件名" extends Vue{
        get ValA(){
            return 1;
        }
    }
</script>
```

#### Emit

@Emit()不传参数,那么它触发的事件名就是它所修饰的函数名.
@Emit(name: string),里面传递一个字符串,该字符串为要触发的事件名.

```vue
<script lang="ts">
    import {Vue, Component, Emit} from 'vue-property-decorator';

    @Component({})
    export default class "组件名" extends Vue{
        mounted(){
            this.$on('emit-todo', function(n) {
                console.log(n)
            })

            this.emitTodo('world');
        }

            @Emit()
        emitTodo(n: string){
            console.log('hello');
        }
    }
</script>
```

#### Watch

```vue
<script>
import {Vue, Component, Watch} from 'vue-property-decorator';

@Watch('child')
onChangeValue(newVal: string, oldVal: string){
    // todo...
}

@Watch('person', {immediate: true, deep: true})
onChangeValue(newVal: Person, oldVal: Person){
    // todo...
}
</script>
```

#### Prop

```vue
<script lang="ts">
    import {Vue, Component, Prop} from 'vue-property-decorator';

    @Component({})
    export default class "组件名" extends Vue{
        @Prop(Number) propA!: number;
        @Prop({default: 'default value'}) propB!: string;
        @propC([String, Boolean]) propC: string | boolean;
    }
</script>
```

#### Mixins

一种是vue-class-component提供的.

```vue
<script>
import  Component  {mixins}  from 'vue-class-component';
import myMixins from 'mixins.ts';

@Component
// 直接extends myMinxins 也可以正常运行
export class myComponent extends mixins(myMixins) {
      created(){
          console.log(this.value) // => Hello
    }
}
</script>
```

第二种方式是在@Component中混入.

```vue
<script>
import { Vue, Component, Prop } from 'vue-property-decorator';
import myMixins from '@static/js/mixins';

@Component({
    mixins: [myMixins]
})
export default class myComponent extends Vue{
    created(){
        console.log(this.value) // => Hello
    }
}
</script>
```
