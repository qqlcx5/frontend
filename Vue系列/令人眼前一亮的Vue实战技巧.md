# 令人眼前一亮的Vue实战技巧
## 前言
本文主要介绍日常项目开发过程中的一些技巧，不仅可以帮助提升工作效率，还能提高应用的性能。以下是我总结一些平时工作中的经验。

## minxin 让组件复用灵活化（待完善）
Vue提供了minxin这种在组件内插入组件属性的方法，个人建议这货能少用就少用，但是有个场景则非常建议使用minxin：当某段代码重复出现在多个组件中，并且这个重复的代码块很大的时候，将其作为一个minxin常常能给后期的维护带来很大的方便。

局部混入  不建议大家用全局混入 写好注释

##  拯救繁乱的template--render函数（待完善）
- template里存在一值多判断
- 代码冗亲，代码杂乱

## 一劳永逸的组件注册
组件使用前，需要引入后再注册：
```JavaScript
import BaseButton from './baseButton'
import BaseIcon from './baseIcon'
import BaseInput from './baseInput'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```
现在 BaseButton、 BaseIcon和BaseInput都可以在模板中使用了：
```JavaScript
<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```
但如果组件多了后，每次都要先导入每个你想使用的组件，然后再注册组件，便会新增很多代码量！我们应该如何优化呢？

这时，我们需要借助一下webpack的require.context() 方法来创建自己的（模块）上下文，从而实现自动动态require组件。这个方法需要3个参数：要搜索的文件夹目录，是否还应该搜索它的子目录，以及一个匹配文件的正则表达式。

我们先在components文件夹（这里面都是些高频组件）添加一个叫global.js的文件，在这个文件里使用require.context 动态将需要的高频组件统统打包进来。然后在main.js文件中引入global.js的文件。
```JavaScript
//  global.js文件
import Vue from 'vue'
function changeStr (str) {
  return str.charAt(0).toUpperCase() + str.slice(1)
}
const requireComponent = require.context('./', false, /\.vue$/)
// 查找同级目录下以vue结尾的组件
const install = () => {
  requireComponent.keys().forEach(fileName => {
    let config = requireComponent(fileName)
    console.log(config) // ./child1.vue 然后用正则拿到child1
    let componentName = changeStr(
      fileName.replace(/^\.\//, '').replace(/\.\w+$/, '')
    )
    Vue.component(componentName, config.default || config)
  })
}
export default {
  install // 对外暴露install方法
}
```
```
// main.js
import index from './components/global.js'
Vue.use(index)
```
最后我们就可以随时随地在页面中使用这些高频组件，无需再手动一个个引入了。

##  小项目Vuex的替代方案--Vue.observable()（待完善）

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。但如果我们开发非大型单页应用，使用 Vuex 可能是繁琐冗余的，有点大材小用了。所以如果说小项目的状态管理，我们不妨用2.6版本新增`Vue.observable()` ，通过使用这个 api 我们可以应对一些简单的跨组件数据状态共享。




## 隐藏的大招--hook
开发过程中我们有时候要创建一个定时器，在组件被销毁之前，这个定时器也要销毁。代码如下：
```JavaScript
mounted() {
  // 创建一个定时器
    this.timer = setInterval(() => {
      // ......
    }, 500);
  },
  // 销毁这个定时器。
  beforeDestroy() {
    if (this.timer) {
      clearInterval(this.timer);
      this.timer = null;
    }
  }
```
这种写法有个很明显的弊端：定时器timer的创建和清理并不是在一个地方，这样很容易导致忘记去清理！

我们可以借助hook对代码整合，这样代码也更容易维护了：

```JavaScript
mounted() {
    let timer = setInterval(() => {
      // ......
    }, 500);
    this.$once("hook:beforeDestroy", function() {
      if (timer) {
        clearInterval(timer);
        timer = null;
      }
    });
  }
```

在Vue组件中，可以用过$on,$once去监听所有的生命周期钩子函数，如监听组件的updated钩子函数可以写成 this.$on('hook:updated', () => {})。

hook除了上面的运用外，还可以**外部监听组件的生命周期函数**。在某些情况下，我们需要在父组件中了解一个子组件何时被创建、挂载或更新。

比如，如果你要在第三方组件 CustomSelect 渲染时监听其 updated 钩子，可以通过`@hook:updated`来实现：
```JavaScript
<template>
  <!--通过@hook:updated监听组件的updated生命钩子函数-->
  <!--组件的所有生命周期钩子都可以通过@hook:钩子函数名 来监听触发-->
  <custom-select @hook:updated="doSomething" />
</template>
<script>
import CustomSelect from "../components/custom-select";
export default {
  components: {
    CustomSelect
  },
  methods: {
    doSomething() {
      console.log("custom-select组件的updated钩子函数被触发");
    }
  }
};
</script>
```

## 简单暴力的router key
我们在项目开发时，可能会遇到这样问题:当页面切换到同一个路由但不同参数地址时，比如/detail/1，跳转到/detail/2，页面跳转后数据竟然没更新？路由配置如下：
```JavaScript
 {
     path: "/detail/:id",
     name:"detail",
     component: Detail
 }
```
这是因为vue-router会识别出两个路由使用的是同一个组件从而进行复用，并不会重新创建组件，而且组件的生命周期钩子自然也不会被触发，导致跳转后数据没有更新。那我们如何解决这个问题呢？
我们可以为router-view组件添加属性key,例子如下：
```
<router-view :key="$route.fullpath"></router-view>
```
这种办法主要是利用虚拟DOM在渲染时候通过key来对比两个节点是否相同，如果key不相同，就会判定router-view组件是一个新节点，从而先销毁组件，然后再重新创建新组件，这样组件内的生命周期会重新触发。

##  高精度权限控制--自定义指令directive
我们通常给一个元素添加v-if / v-show，来判断该用户是否有权限，但如果判断条件繁琐且多个地方需要判断，这种方式的代码不仅不优雅而且冗余。针对这种情况，我们可以封装了一个指令权限，能简单快速的实现按钮级别的权限判断。
我们先在新建个array.js文件，用于存放与权限相关的全局函数
```javascript
// array.js
export function checkArray (key) {
  let arr = ['admin', 'editor']
  let index = arr.indexOf(key)
  if (index > -1) {
    return true // 有权限
  } else {
    return false // 无权限
  }
}
```
然后在将array文件挂载到全局中 
```javascript
// main.js
import { checkArray } from "./common/array";
Vue.config.productionTip = false;
Vue.directive("permission", {
  inserted (el, binding) {
    let permission = binding.value; // 获取到 v-permission的值
    if (permission) {
      let hasPermission = checkArray(permission);
      if (!hasPermission) { // 没有权限 移除Dom元素
        el.parentNode && el.parentNode.removeChild(el);
      }
    }
  }
});
```
最后我们在页面中就可以通过自定义指令 v-permission来判断：
```javascript
 <div class="btns">
    <button v-permission="'admin'">权限按钮1</button>  // 会显示
    <button v-permission="'visitor'">权限按钮2</button> //无显示
    <button v-permission="'editor'">权限按钮3</button> // 会显示
  </div>
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c011bd56b3224668a34e94bcbc35bdf7~tplv-k3u1fbpfcp-zoom-1.image)

## 动态指令参数
Vue 2.6的最酷功能之一是可以将指令参数动态传递给组件。我们可以用方括号括起来的 JavaScript 表达式作为一个指令的参数：
```javascript
<a v-bind:[attributeName]="url"> 这是个链接 </a>
```
这里的 attributeName 会被作为一个 JavaScript 表达式进行动态求值，求得的值将会作为最终的参数来使用。
同样地，你可以使用动态参数为一个动态的事件名绑定处理函数：
```javascript
<a v-on:[eventName]="doSomething"> 这是个链接 </a>
```
接下来我们看个例子：假设你有一个按钮，在某些情况下想监听单击事件，在某些情况下想监听双击事件。这时动态指令参数派上用场：
```javascript
<template>
  <div>
    <aButton @[someEvent]="handleSomeEvent()" />
  </div>
</template>
<script>
export default {
  data () {
    return {
      someEvent: someCondition ? "click" : "dbclick"
    }
  },
  methods: {
    handleSomeEvent () {
      // handle some event
    }
  }
}
</script>
```
##  过滤器让数据处理更便利
Vue.js 允许你自定义过滤器，它的用法其实是很简单，但是可能有些朋友没有用过，接下来我们介绍下：
### 1.理解过滤器
- 功能：**对要显示的数据进行特定格式化后再显示**。
- 注意：**过滤器并没有改变原本的数据，需要对展现的数据进行包装**。
- 使用场景：双花括号插值和 v-bind 表达式 (后者从 2.1.0+ 开始支持)。
### 2.定义过滤器
可以在一个组件的选项中定义本地的过滤器：
```
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```
也可以在创建 Vue 实例之前全局定义过滤器：
```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})
```

### 3.使用过滤器
使用方法也简单，即在双花括号中使用管道符(pipeline) |隔开
```JavaScript
<!-- 在双花括号中 -->
<div>{{ myData| filterName}}</div>
<div>{{ myData| filterName(arg)}}</div>
<!-- 在 v-bind 中 -->
<div v-bind:id="rawId | formatId"></div>
```
过滤器可以串联：
```
{{ message | filterA | filterB }}
```
在这个例子中，filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中。
接下来我们看个如何使用过滤器格式化日期的例子：
```JavaScript
 <div>
    <h2>显示格式化的日期时间</h2>
    <p>{{ date }}</p>
    <p>{{ date | filterDate }}</p>
    <p>年月日: {{ date | filterDate("YYYY-MM-DD") }}</p>
 </div>
 ......
  filters: {
    filterDate(value, format = "YYYY-MM-DD HH:mm:ss") {
      console.log(this)//undefined 过滤器没有this指向的
      return moment(value).format(format);
    }
  },
  data() {
    return {
      date: new Date()
    };
  }
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a3add3374b44789a7debd8ea2ac7761~tplv-k3u1fbpfcp-zoom-1.image)





## 参考文章与书籍
- [Vue官方文档](https://cn.vuejs.org/v2/guide/reactivity.html)
- [Vue2.6查漏补缺](https://www.yuque.com/u40512/in31e0/abczpz#gMIye)
- [Vue.js最佳实践（五招让你成为Vue.js大师)](https://segmentfault.com/a/1190000014085613)
- [实战技巧，Vue原来还可以这样写](https://juejin.im/post/6844904196626448391#heading-1)
- [使用Vue.observable()进行状态管理](https://segmentfault.com/a/1190000019292569)
- [Vue 项目构建与开发入门](https://juejin.im/book/6844733734143393805/section/6844733734193725448)
- [2020年的12个Vue.js开发技巧和窍门](https://segmentfault.com/a/1190000022341733)


