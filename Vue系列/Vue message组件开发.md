# message
```js
import Vue from 'vue';
import messageComp from './message.vue';
let MessageConstructor = Vue.extend(messageComp);
const Message = options => {
  let instance = new MessageConstructor({
    data: options
  });
  instance.$mount();
  document.body.appendChild(instance.$el);
  instance.visible = true;
};
['success', 'error', 'warning'].forEach(type => {
  Message[type] = function(options) {
    options.type = type;
    return Message(options);
  };
});
export { Message };

```


```vue
<template>
  <transition name="fade">
    <div v-if="visible">hello {{ message }} {{ type }}</div>
  </transition>
</template>
<script>
export default {
  data() {
    return {
      message: '',
      type: true,
      visible: false,
      duration: 3000
    };
  },
  mounted() {
    if (this.duration > 0) {
      this.timer = setTimeout(() => {
        this.visible = false;
        this.$destroy(); // 销毁实例
        this.$el.parentNode.removeChild(this.$el); // 销毁组件
      }, this.duration);
    }
  }
};
</script>
<style lang="scss" scoped>
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.fade-enter-active,
.fade-leave-active {
  transition: all 1s linear;
}
</style>

```