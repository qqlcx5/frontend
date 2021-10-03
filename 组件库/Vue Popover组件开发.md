---
title: Vue Popover组件开发
date: 2021-5-29
tags:
- vue
---
```vue

<template>
  <div class="lcx-popover">
    <div
      class="lcx-popover-content"
      :class="`content-${placement}`"
      v-if="visible"
      ref="content"
      @click.stop
    >
      <h3>{{ title }}</h3>
      {{ content }}
      <i class="arrow"></i>
    </div>
    <slot name="reference"></slot>
  </div>
</template>
<script>
const on = (element, event, handle) => {
  element.addEventListener(event, handle, false);
};
const off = (element, event, handle) => {
  element.removeEventListener(event, handle, false);
};
export default {
  name: 'lcx-popover',
  data() {
    return {
      visible: false,
    };
  },
  watch: {
    visible(val) {
      if (val) {
        this.$nextTick(() => {
          let content = this.$refs.content;
          document.body.appendChild(content);
          if (this.trigger === 'hover') {
            on(content, 'mouseenter', this.handleMouseEnter);
            on(content, 'mouseleave', this.handleMouseLeave);
          }
        });
      }
    },
  },
  props: {
    title: {
      type: String,
    },
    trigger: {
      type: String,
    },
    width: {
      type: String,
    },
    content: {
      type: String,
    },
    placement: {
      type: String,
      validator(type) {
        if (!['top', 'left', 'right', 'botton'].includes(type)) {
          throw new Error(
            '属性必须是' + ['top', 'left', 'right', 'botton'].join('、')
          );
        }
        return true;
      },
    },
  },
  methods: {
    handleToggle() {
      this.visible = !this.visible;
    },
    handleDocumentToggle(e) {
      if (this.$el.contains(e.target)) return;
      this.visible = false;
    },
    handleMouseEnter() {
      clearTimeout(this.timer);
      this.visible = true;
    },
    handleMouseLeave() {
      this.timer = setTimeout(() => {
        this.visible = false;
      }, 200);
    },
  },
  mounted() {
    let reference = this.$slots.reference;
    if (reference) {
      this.reference = reference[0].elm;
    }
    if (this.trigger === 'hover') {
      on(this.$el, 'mouseenter', this.handleMouseEnter);
      on(this.$el, 'mouseleave', this.handleMouseLeave);
    } else if (this.trigger === 'click') {
      on(this.reference, 'click', this.handleToggle);
      on(document, 'click', this.handleDocumentToggle);
    }
  },
  beforeDestroy() {
    off(this.$el, 'mouseenter', this.handleMouseEnter);
    off(this.$el, 'mouseleave', this.handleMouseLeave);
    off(this.reference, 'click', this.handleToggle);
    off(document, 'click', this.handleDocumentToggle);
  },
};
</script>
<style lang="scss" scoped>
.lcx-popover {
  display: inline-block;
  position: relative;
  &-content {
    position: absolute;
    top: 100px;
    left: 30px;
    padding: 15px;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    background-color: #fff;
  }
}
.arrow {
  &::before {
    position: absolute;
    display: block;
    content: '';
    width: 0;
    height: 0;
    border: 10px solid transparent;
    border-bottom-color: #fff;
    filter: drop-shadow(0 -2px 1px #eee);
  }
  // &::after {
  //   border-bottom-color: #fff;
  //   transform: translateY(1px);
  //   filter: drop-shadow(0 -2px 1px #ccc);
  // }
}
.content-top{
  .arrow {
    &::before {
      top: -19px;
      left: 50%;
      margin-left: -10px;
    }
  }
}
.content-bottom{
  .arrow {
    &::before {
      bottom: -19px;
      left: 50%;
      margin-left: -10px;
      transform: rotate(-180deg);
    }
  }
}
.content-left{
  .arrow {
    &::before {
      top: 50%;
      left: -19px;
      margin-top: -10px;
      transform: rotate(-90deg);
    }
  }
}
.content-right{
  .arrow {
    &::before {
      top: 50%;
      right: -19px;
      margin-top: -10px;
      transform: rotate(90deg);
    }
  }
}
</style>
```