# input


```vue
<template>
  <div class="lcx-input" :class="inputClass">
    <!-- <slot name='prefix' /> -->
    <input
      :type="showPassword ? (passwordVisible ?type : 'password' ) : type"
      :name="name"
      :value="value"
      :placeholder="placeholder"
      :disabled="disabled"
      @input="$emit('input', $event.target.value)"
      class="lcx-input__inner"
      ref="input"
    />
    <!-- <slot name='suffix' /> -->
    <!-- native 给原生绑定事件 不加给组件绑定事件 -->
    <!-- <div
      class="clearable"
      v-if="clearable"
      @click="$emit('input', '')"
      @mousedown.prevent
    >
      X
    </div> -->
    <lcx-icon class="clearable" icon="icon-guanbi" v-if="clearable" @click.native="$emit('input', '')" @mousedown.native.prevent />
    <lcx-icon class="showPassword" icon="icon-icon-test" v-if="showPassword" @click.native="changeStatus" />
  </div>
</template>

<script>
export default {
  name: 'lcx-input',
  props: {
    value: {
      type: String,
      default: null,
    },
    name: {
      type: String,
      default: null,
    },
    placeholder: {
      type: String,
      default: '请输入内容',
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    type: {
      type: String,
      default: 'text',
    },
    clearable: {
      type: Boolean,
      default: false,
    },
    showPassword: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    inputClass() {
      const classes = [];
      if (this.clearable || this.showPassword) {
        classes.push(`lcx-input--suffix`);
      }
      // if (this.clearable || this.showPassword) {
      //   classes.push(`lcx-input--prefix`);
      // }
      return classes;
    },
  },
  data() {
    return {
      passwordVisible: false,
    };
  },
  methods:{
    changeStatus(){
       this.passwordVisible = !this.passwordVisible
       this.$nextTick(()=>{
        this.$refs.input.focus()
       })
    }
  }
};
</script>
<style lang="scss" scoped>
.lcx-input {
  position: relative;
  font-size: 14px;
  display: inline-block;
  width: 180px;
  &.lcx-input--prefix{
    .lcx-input__inner {
      padding: 0 30px 0 15px;
    }
  }
  &.lcx-input--suffix{
    .lcx-input__inner {
      padding: 0 30px 0 15px;
    }
  }
  &.lcx-input--prefix.lcx-input--suffix{
    .lcx-input__inner {
      padding: 0 30px;
    }
  }
  .lcx-input__inner {
    -webkit-appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    box-sizing: border-box;
    color: #606266;
    display: inline-block;
    font-size: inherit;
    height: 40px;
    // line-height: 40px;
    outline: none;
    padding: 0 15px;
    transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
    width: 100%;
    &[disabled] {
      background-color: #f5f7fa;
      border-color: #e4e7ed;
      color: #c0c4cc;
      cursor: not-allowed;
    }
  }
  .clearable {
    position: absolute;
    right: 14.5px;
    top: 15px;
    font-size: 12px;
    cursor: pointer;
  }
  .showPassword {
    position: absolute;
    right: 10px;
    top: 12px;
    font-size: 16px;
    cursor: pointer;
  }
}
</style>

```