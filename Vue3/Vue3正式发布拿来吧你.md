---
title: Vue 3.2正式发布，拿来吧你
date: 2021-7-28
---

## 前言
![1](https://wakatime.com/share/@b25261ab-63cd-49bc-a4f3-2de7cb963339/e9b93c6c-766d-4bca-ad13-24d8be72461a.svg)

8 月 5 日，Vue.js 作者尤雨溪在博客上宣布 Vue.js 3.2 版本正式发布。 本文的相关内容为中文翻译。

> 原文地址：https://blog.vuejs.org/posts/vue-3.2.html

![-w612](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8f65ac2dad8452dbc2460c4333e1c5b~tplv-k3u1fbpfcp-zoom-1.image)

我们很高兴地宣布 “Vue.js 3.2” 的发布！此版本包括许多重要的新功能和性能改进，并且不包含重大更改。



### 新的SFC功能

关于单文件组件（SFC，即.vue 文件）的两项功能已经由实验状态正式毕业，现提供稳定版本：

* `<script setup>` 是一种编译时语法糖，可在 SFC 内使用 Composition API 时极大地改善开发者体验。

* `<style> v-bind`在 SFC`<style>`标签中启用组件状态驱动的动态 CSS 值。

下面是这两个新功能的示例

```vue
<script setup>
import { ref } from 'vue'

const color = ref('red')
</script>

<template>
  <button @click="color = color === 'red' ? 'green' : 'red'">
    Color is: {{ color }}
  </button>
</template>

<style scoped>
button {
  color: v-bind(color);
}
</style>
```

感兴趣的朋友，可以阅读它们的文档：
* [https://v3.vuejs.org/api/sfc-script-setup.html](https://v3.vuejs.org/api/sfc-script-setup.html)
* [https://v3.vuejs.org/api/sfc-style.html#state-driven-dynamic-css](https://v3.vuejs.org/api/sfc-style.html#state-driven-dynamic-css)

在此基础上`<script setup>`，我们还有一个新的 RFC，通过启用编译器的糖改进 ref 使用体验。

### Web组件

Vue 3.2 引入了`defineCustomElement`一种使用 Vue 组件 API轻松创建原生自定义元素的新方法：


```
import { defineCustomElement } from 'vue'

const MyVueElement = defineCustomElement({
  // 普通Vue组件选项
})

// 注册自定义元素。
// 注册后，所有`<my-vue-element>` 标签
// 页面上的将被升级。
customElements.define('my-vue-element', MyVueElement)
```

这个 API 允许开发者创建 Vue 驱动的 UI 组件库，这些库可以与任何框架一起使用，或者根本没有框架。我们还在我们的文档中添加了一个关于在 Vue 中使用和创建 Web 组件的新章节。

### 性能改进

由于`@basvanmeurs`的出色工作，3.2 对 Vue 的反应性系统进行了一些重大的性能改进。具体来说：
* 更高效的 ref 实现（约 260% 的读取速度/约 50% 的写入速度）
* 提升约 40%依赖项跟踪速度
* 内存使用量减少约 17%

模板编译器也得到了一些改进：

* 创建普通元素 VNode 的速度提高了约 200%
* 更积极的持续提升hoisting [1][2]

最后，有一个新的 v-memo 指令，它提供了记忆模板树的一部分的能力。命中允许 Vue 不仅跳过虚拟 DOM 差异，而且完全跳过新 VNode 的创建。虽然很少需要，但它提供了一个逃生舱来在某些情况下挤出最大性能，例如大型列表. v-memo v-for

使用单行添加，让 Vue 成为 `js-framework-benchmark`中最快的主流框架之一：
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/abe88a28efb24a2db6a6e8952d832168~tplv-k3u1fbpfcp-zoom-1.image)

### 服务端渲染
`@vue/server-renderer3.2` 中的包现在提供了一个 ES 模块构建，它也与 Node.js 内置模块分离。
如此一来可以通过`@vue/server-renderer`在非 Node.js环境运行（例如CloudFlare Workers或 Service Workers）。

我们还改进了流式渲染 API，提供了用于渲染到Web Streams API 的新方法。查看文档以`@vue/server-renderer`获取更多详细信息。

### Effect Scope API
3.2 引入了一个新的`Effect Scope API`，用于直接控制反应性效果（计算和观察者）的处理时间。它可以更轻松地在组件上下文之外利用 Vue 的响应式 API，并且还解锁了组件内部的一些高级用例。

这是主要面向库作者的低级 API，因此建议阅读该功能的RFC以了解此功能的动机和用例。

有关 3.2 中所有更改的详细列表，请参阅完整的更改日志。

