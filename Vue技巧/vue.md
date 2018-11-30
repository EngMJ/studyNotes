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

> Vue.config.ignoredElements
* 忽略检查template内的非HTML元素及组件元素

> Vue.config.keyCodes
* 设置按键的键值/别名

> Vue.extend()
* 设置vue组件实例

> data
* 变量开头为_或$的,需在vm.$data._property中访问

> delimiters
* 改变插值表达式的字符串

> functional
* 函数式组件

> model
* 改变v-model所绑定的事件及获取的属性

> inheritAttrs
* 控制是否显示未注释props属性,在元素标签上是否显示

> $options
* 获取实例上的自定义属性

> $attr
* 获取父组件所有props属性(包括未注册)

> $listeners
* 获取父组件所有的事件监听

> $forceUpdate
* 强制当前实例组件和具有插槽的子组件更新渲染,不会影响所有子组件

**指令**
> v-bind
* .prop 隐藏元素绑定属性的键值
* .sync 为属性自动绑定一个update事件
* .camel 将烤串命名改为驼峰命名

> v-pre
* 不编译

> v-cloak
* 至编译结束都会关联该元素
* 场景: 编译时显示vue变量,通过该指令配合css设置隐藏
```
<template>
    <div v-cloak>
      {{ message }}
    </div>
</template>

<style>
    [v-cloak] {
      display: none!importtant;
    }
</style>
```

> v-once
* 编译一次后不更新, 对于大量静态内容的元素/组件使用

**插件**
> provide/inject ...不知道

**特殊属性**
> key
* key更新组触发重新渲染,重新进行完整的生命周期

> is
* 动态组件的属性, 代表动态组件所表示的对应组件
```
<template>
    <component is='组件名/实例'></component>
</template>
```

**内置组件**
> keep-alive
* 缓存组件或路由视图
* 可激活activated/deactivated的Hooks
* 属性:
* include匹配对应name组件
* exclude不匹配对应name组件
* max 最大缓存组件数量
```
<keep-alive>
    <component is='currentView'></component>
</keep-alive>
```

**Hooks**
> activated
* keep-alive的组件激活时触发

> deactivated
* keep-alive的组件隐藏时触发
