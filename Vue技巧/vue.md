## Vue.js
#### Vue-API

> Hooks
* 声明式
```
// 声明式
export default {
 ...
 created: [
   function one () {
     console.log(1)
   },
   function two () {
     console.log(2)
   }
 ]
 ...
}
// 生命周期函数可顺序执行数组
```

* 属性式
```
<!-- Child.vue -->
<template>
  <h3>I'm child!</h3>
</template>

<!-- Parent.vue -->
<template>
 <child @hook:created="handleChildCreated"></child>
</template>

<script>
   import Child from './child.vue'
   export default {
     components: [ Child ],
     methods: {
       handleChildCreated () {
         console.log('handle child created...')
       }
     }
   }
</script>

```

> Mixins
* 场景:复用props/methods等情况
```
// paging-mixin.vue
export default {
  props: {
    pageSize: 1,
    pageLength: 10,
    currentPage: 1
    total: 20
  },
  methods: {
    /**
     * 上一页
     */
    prevPage (page) {
      ...
    },
    /**
     * 下一页
     */
    nextPage (page) {
      ...
    }
    /**
     * 跳转到当前页
     */
    currentPage (page) {
      ...
    }
  }
}
```
```
<template>
  <div class="user-model">
    <my-table :data="users"></my-table>
    <my-paging
      :page-length="pageLength"
      :page-size="pageSize"
      :current-page="currentPage"
      :total="total">
    </my-paging>
  </div>
</template>

<script>
  import PagingMixin from '../mixins/paging-mixin.vue'
  export default {
    mixins: [PagingMixin],
    data () {
      return {
        users: [],
        pageLength: 10,
        pageSize: 1,
        currentPage: 1,
        total: 20
      }
    }
  }
</script>
```

> v-once
* 场景: 静态数据缓存
```
<template>
  <div class="box" v-once>
    <h2> 用户协议 </h2>
    ... a lot of static content ...
  </div>
</template>
```
