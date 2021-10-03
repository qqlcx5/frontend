---
title: Vue button组件开发
date: 2021-5-29
categories:
 - frontEnd
tags:
- vue
---

```vue
<template>
  <button class="lcx-button" :class="btnClass">
    <lcx-icon :icon="icon" v-if="icon" />
    <slot></slot>
  </button>
</template>
<script>
export default {
  name: 'lcx-button',
  props: {
    type: {
      type: String,
      default: '',
      validator(type) {
        if (
          type &&
          !['primary', 'success', 'info', 'danger', 'warning'].includes(type)
        ) {
          // eslint-disable-next-line no-console
          console.error(
            'type类型必须为其中一种' +
              ['primary', 'success', 'info', 'danger', 'warning'].join('、')
          );
          return false;
        }
        return true;
      }
    },
    icon: {
      type: String,
      default: ''
    }
  },
  computed: {
    btnClass() {
      let classes = [];
      if (this.type) {
        classes.push(`lcx-button-${this.type}`);
      }
      return classes;
    }
  }
};
</script>
<style lang="scss" scoped>
@import '@/assets/css/_var.scss';
.lcx-button {
  display: inline-block;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  color: #606266;
  -webkit-appearance: none;
  text-align: center;
  box-sizing: border-box;
  margin: 0;
  padding: 12px 20px;
  font-size: 16px;
  border-radius: 4px;
  border: 1px solid #dcdfe6;
  background-color: #fff;
  &:hover {
    color: $primary;
    border-color: #c6e2ff;
    background-color: #ecf5ff;
  }
  &:focus,
  &:active {
    color: $primary;
    border-color: #c6e2ff;
    background-color: #ecf5ff;
    outline: none;
  }
  ::v-deep .icon {
    margin-right: 5px;
    color: #fff;
  }

  @each $type,
    $color
      in (
        primary: $primary,
        success: $success,
        info: $info,
        danger: $danger,
        warning: $warning
      )
  {
    &-#{$type} {
      color: #fff;
      background-color: $color;
    }
  }
  @each $type,
    $color
      in (
        primary: $primary-background-hover,
        success: $success-background-hover,
        info: $info-background-hover,
        danger: $danger-background-hover,
        warning: $warning-background-hover
      )
  {
    &-#{$type}:hover {
      color: #fff;
      background-color: $color;
      border-color: $color;
    }
  }

  @each $type,
    $color
      in (
        primary: $primary-background-active,
        success: $success-background-active,
        info: $info-background-active,
        danger: $danger-background-active,
        warning: $warning-background-active
      )
  {
    &-#{$type}:active,
    &-#{$type}:focus {
      color: #fff;
      background-color: $color;
      border-color: $color;
    }
  }
}
</style>

```