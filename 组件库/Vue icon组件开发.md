---
title: Vue icon组件开发
date: 2021-5-29
categories:
 - frontEnd
tags:
- vue
---

```vue
<template>
  <svg class="icon" aria-hidden="true" :class="svgClass">
    <use :xlink:href="`#${icon}`"></use>
  </svg>
</template>
<script>
export default {
  name: 'lcxIcon',
  props: {
    icon: {
      type: String,
      required: true
    },
    svgClass: {
      type: String
    }
  }
};
</script>
<style lang="scss" scoped>
.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
  color: #999;
}
</style>

```