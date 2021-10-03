---
title: 自动部署到GitHub
date: 2018-12-28
tags:
- VuePress
---
## 前言

在一次意外看到`Vue3.0`课程打折，而且还是个大牛在教，我被贪图小利的驱使下，购买了这个课程，然后想着课程都买了不看浪费。图了让自己良心过意得去进去看看，经过大牛讲解，我被`Composition API`等新写法震撼到了。在未接触`Vue3.0`之前我听说`Vue3.0`模仿 `React`等其他语言，很多API进行重大重构，已经不像Vue，我在想如果Vue3.0学习成本很高的话，不如去学学`React`看看。原因`React`用户基数多，大型项目完善，找工作也更容易等。在没规划的学习`React`一段时间后，感觉自己学到一些皮毛，而且学的很慢，可能方法没找对。

被`Vue3.0`等新的语法震撼到后，我去看`Composition API`官网和相关学习资料后，决定认真去学`Vue3.0`，在学了一两个小时后，发现`Vue3.0`比想象中更加简单。网上这方面的教程估计也很少，很多是介绍`Vue3.0`新特性这类的吧（至少我看好几篇都是这样的）。Vue3.0用法主要还是`Composition API`为主，学会了估计Vue3.0也就差不多了。（2020/7/21补充，虽然用法简单，但是完整的vue3.0实战还是需要等待一段时间，毕竟生态圈还在开发中，比如vue3.0需要使用`vue-router4.xx` `keep-alive`等现阶段还在开发中，急于使用的话，一些功能实现的话需要vue2.0vue3.0混合使用吧。）

### Vue3.0 几大优点

- `Performance`：相对于Vue2.0性能显著提升。
- `Tree shaking support`：可以将无用模块“剪辑”，仅打包需要的。
- `Composition API`：组合API
- `Fragment`, `Teleport`, `Suspense`：`Fragment`
- 不在限制模版中的单个根节点。`Teleport` 传送门。Suspense “悬念”。
- `Better TypeScript support`：更友好的的TypeScript支持。
`Custom Renderer API`：暴露了自定义渲染API——用在WebGL渲染器等。

### 性能 `Performance`

- 重写的虚拟Dom实现
- 编译器获知的快速执行路径。
- 更高效的组件初始化
- `update` 1.3 ~ 2倍 的性能提升
- SSR速度2 ~ 3倍

> Vue目前稳定版本2.6.x，预计8月份上线2.7.x版本（增加Vue3相关API等）。

### 如何学习 Vue3.0

接下来，介绍Vue3.0和Vue2.0之间的差别和经验之谈，菜鸟一枚，大言不惭在各位面前献丑了。熟悉以下知识点估计面试、项目实战也木问题。

- `Composition API`
- Vue 3.0相关的原理。

### Composition API

前往官网，[Vue Composition API](https://composition-api.vuejs.org/zh/api.html)
预计8月份左右正式版发布了，`Vue Composition API`用法基本上是板上钉钉的事，几乎不会做太大的调整，现在先熟悉一下，等生态完善入手更快。

废话不多说了，直接开始干。

#### `Options API` VS `Composition API`

Vue2.0 `Options API`左图，Vue3.0 `Composition API`右图

![-w966](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200717000441.jpg)


如图所示，Vue2.0 `Options API` 转换成 Vue3.0 `Composition API` 的 `setup() {}`东西，然后抛出我们需要的属性名。

总结：`Composition API`好处

- 相关逻辑的代码组织起来，提高可维护性和可读性。
- 更好重用逻辑代码。（哈哈哈，mixins可以废用）



#### setup

setup 函数是一个新的组件选项。作为在组件内使用 Composition API 的入口点。

**生命周期**

初始化 `props`，在 `beforeCreate` 钩子之间调用。

**注意事项**

从上面生命周期可以得到，`beforeCreate`时候，`data`还未生成，`this` 在`setup()`中不可用。

`setup` 可以接受 `props` `context` 参数

```js
  setup(props) { 
    // 输出的值 Proxy类型
    console.log(props);
    
    如果父组件的props更新，console.log并不会再次输出的值。需要调用 watchEffect 函数.
    watchEffect(()=>{
        console.log(props);
    })
    
  }
```

`watchEffect` 立即执行传入的一个函数，并响应式追踪其依赖，并在其依赖变更时重新运行该函数。

> 个人建议：不要将Vue3.0想成新东西，这样学习就有排斥心理。而应该想成换汤不换药的东西，像披着马甲的`Vue`。我想这样更加有助于学习吧。

#### 响应式数据 VS

Vue2.0 写在`data`里面属性名，在当前组件就可以直接引用，数据改变就会更新视图。
```js
  data() {
    return {};
  },
```
Vue3.0 需要通过 `ref` 或者 `reactive` 来声明响应式数据。
> `ref`接受一个参数值并返回一个响应式且可改变的 `ref` 对象。 而不是Vue2.0 `ref` 被用来给元素或子组件注册引用信息。在这说明此`ref`非彼`ref`不要混淆。



**进阶：**

Vue2.0 的响应式原理基于 `Object. defineproperty` 实现。

Vue3.0 `ref` 响应式原理基于 `Object. defineproperty`，而 `reactive` 响应式原理则我们常说的 `Proxy`。

`ref(0)` 刚接触时候`ref`里面`0`是什么鬼，还以为Vue2.0某个DOM或组件，后来了解到 0 只是个响应式初始值，而且也可以是个对象。如 `ref({ count: 1})`等，因为基于`Object. defineproperty` 就有了给对象新增属性和修改数组数据缺陷。

#### 模板 Refs

我上面说过Vue3.0 全面兼容Vue2.0，那么Vue2.0的 `ref` API，又该如何调用那。

```vue
<template>
  <div ref="root"></div>
</template>

<script>
  import { ref, onMounted } from 'vue'

  export default {
    setup() {
      const root = ref(null)

      onMounted(() => {
        // 在渲染完成后, 这个 div DOM 会被赋值给 root ref 对象
        console.log(root.value) // <div/>
      })

      return {
        root,
      }
    },
  }
</script>
```

看到上面这段代码，真的佩服尤大大的脑洞，设计出 扩展`ref`，且和设计理念想符合，丝毫没有增加新的学习成本，也不用担心版本一样，混淆用法。

#### 函数调用

写了一个例子来做对比，Vue2.0 和Vue3.0是如何调用的。

**vue2.0**

```html
<template>
  <div>
    {{total}}
    <div @click="handleClick(0)">减</div>
    <div @click="handleClick(1)">加</div>
  </div>
</template>
<script>
export default {
  data() {
    return { total: 0 };
  },
  methods: {
    handleClick(type){
      type === 1 ? this.total++ :this.total--
    }
  },
};
</script>
```

**Vue3.0**

```html
<template>
    {{total}}
    <div @click="handleClick(0)">减</div>
    <div @click="handleClick(1)">加</div>
</template>
<script>
import { ref, reactive } from "vue";

export default {
  setup(props) {
    let total = ref(0);
    // reactive 基于Proxy对数据深度监听，声明响应式数据
    let newTotal = reactive({
      total: 0,
      arr: [0]
    })
    function handleClick(type){
      type === 1 ? total.value ++ : total.value --
      // reactive 写法
      type === 1 ? newTotal.total++ : newTotal.total--
      newTotal.arr[0] = newTotal.arr[0] + 1
    }
    return {
      total,
      handleClick
    };
  }
};
</script>
```

细品上面的区别

 - ref 对象拥有一个指向内部值的单一属性 `.value` (需注意)，在模版中使用，无需 `.value`。
 - 模版中的不在限制单个根节点。
 - reactive 支持`arr[0] = arr[0] + 1`,不需要如vue2.0 `$set` API 去更新其中的值。


#### computed

简单来说，用法差不多。

**扩展**

Vue3.0 中 `computed` 传入一个`getter`，函数，返回一个不可更改的 ref 对象。
 

**Vue2.0**

```js
  data: {
    return { message: 'Hello' }
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
```

**Vue3.0**

```js
import { ref } from "vue";

export default {
  setup(props) {
    let message = ref('hello world');
    const reversedMessage = computed(() => message.value.split('').reverse().join(''))
    
    return {
      reversedMessage
    };
  }
};
</script>
```

#### watch

watch API 完全等效于 2.x this.$watch （以及 watch 中相应的选项）。watch 需要侦听特定的数据源，并在回调函数中执行副作用。默认情况是懒执行的，也就是说仅在侦听的源变更时才执行回调。

#### 生命周期钩子函数

与 2.x 版本生命周期相对应的组合式 API

- `beforeCreate` -> 使用 `setup()`
- `created` -> 使用 `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeDestroy` -> `onBeforeUnmount`
- `destroyed` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`

新增的钩子函数

除了和 2.x 生命周期等效项之外，组合式 API 还提供了以下调试钩子函数：

- `onRenderTracked`
- `onRenderTriggered`

这两个钩子我暂时还不太清楚，后面了解清楚再补上。
 

### 其他API

以下这些API 用法目测觉得很简单，如有不懂自行官网查阅。

**readonly**

和`Object.freeze()` 方法相似，不能修改值，会提示报错。（哈哈，只能看不能摸）

传入一个对象（响应式或普通）或 ref，返回一个原始对象的只读代理。一个只读的代理是“深层的”，对象内部任何嵌套的属性也都是只读的。

**unref**

如果参数是一个 ref 则返回它的 value，否则返回参数本身。它是 val = isRef(val) ? val.value : val 的语法糖。

**toRef**

toRef 可以用来为一个 reactive 对象的属性创建一个 ref。这个 ref 可以被传递并且能够保持响应性。

**toRefs**

把一个响应式对象转换成普通对象，该普通对象的每个 property 都是一个 ref ，和响应式对象 property 一一对应。

**isRef**

检查一个值是否为一个 ref 对象。

**isProxy**

检查一个对象是否是由 reactive 或者 readonly 方法创建的代理。

**isReactive**

检查一个对象是否是由 reactive 创建的响应式代理。

如果这个代理是由 readonly 创建的，但是又被 reactive 创建的另一个代理包裹了一层，那么同样也会返回 true。

**isReadonly**

检查一个对象是否是由 readonly 创建的只读代理。


### Vue3.0 小总结

- Vue 使用那些API import 引入。
- v-model API 使用反而复杂了。
- 提高可维护性和可读性。
- 入手简单，看了不到一小时，没有实战觉得什么都会。（吹牛）
- 尤大大 写的`vite`非常不错。

参考链接：

[Vue 组合式 API](https://composition-api.vuejs.org/zh/api.html)

[抄笔记：尤雨溪在Vue3.0 Beta直播里聊到了这些…](https://juejin.im/post/5e9f6b3251882573a855cd52)