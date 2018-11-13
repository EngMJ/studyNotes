## Vue.js
> Vue-API

**hooks**
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
