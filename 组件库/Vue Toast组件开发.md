# Vue Toast组件开发

```js
import vue from 'vue';
// 这里就是我们刚刚创建的那个静态组件
import toastComponent from './Toast.vue';
// 返回一个 扩展实例构造器, 关于 vue.extend 有不懂的同学,可以去查看以下官方文档 https://cn.vuejs.org/v2/api/#Vue-extend
const ToastConstructor = vue.extend(toastComponent);
// 定义弹出组件的函数 接收2个参数, 要显示的文本 和 显示时间
function showToast(text, duration = 2000) {
  // 实例化一个 toast.vue
  const toastDom = new ToastConstructor({
    el: document.createElement('div'),
    data() {
      return {
        text: text,
        showWrap: true,
        showContent: true
      };
    }
  });
  // 把 实例化的 toast.vue 添加到 body 里
  document.body.appendChild(toastDom.$el);
  // 提前 250ms 执行淡出动画(因为我们再css里面设置的隐藏动画持续是250ms)
  setTimeout(() => {
    toastDom.showContent = false;
  }, duration - 250);
  // 过了 duration 时间后隐藏整个组件
  setTimeout(() => {
    toastDom.showWrap = false;
  }, duration);
}
// 注册为全局组件的函数
function registryToast() {
  // 将组件注册到 vue 的 原型链里去,
  // 这样就可以在所有 vue 的实例里面使用 this.$toast()
  vue.prototype.$toast = showToast;
}
export { showToast, registryToast };

```



```vue
<template>
  <div class="wrap" v-if="showWrap" :class="showContent ? 'fadein' : 'fadeout'">
    {{ text }}
  </div>
</template>
<style scoped>
.wrap {
  position: fixed;
  left: 50%;
  top: 50%;
  background: rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  max-width: 400px;
  padding: 20px;
  transform: translate(-50%, -50%);
  color: #fff;
}
.fadein {
  animation: animate_in 0.25s;
}
.fadeout {
  animation: animate_out 0.25s;
  opacity: 0;
}
@keyframes animate_in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
@keyframes animate_out {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>

```
