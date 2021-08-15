# vue技巧
> 作者：simonholdorf
> 翻译：浪里行舟
> 原文：https://dev.to/simonholdorf/10-vue-directives-that-make-your-dev-lives-easier-5dm7

## 前言
Vue.js 最大的特点之一就是它有内置的指令，就像最初的 AngularJS 一样(这是人们认为 Vue像 Angular 的原因之一)。 这些指令使开发更加容易，但更好的是，您可以编写自己的自定义指令。在本文中，我将向您展示由Vue 社区制作10个很酷的指令，您可以立即在项目中使用它们，以节省您的时间和精力，并将您的Vue项目提升到一个新的水平。
### 1.Vue-Lazyload
这个项目在Github上有超过5800个Star，Hilongjw的Vue-Lazyload可让你无延迟地懒加载图片！

项目地址：`https://github.com/hilongjw/vue-lazyload`

安装方式：`npm i vue-lazyload -S`
```
<template>
  <img v-lazy="https://www.example.com/example-image.jpg">
</template>
```
效果演示：


![](https://user-gold-cdn.xitu.io/2019/12/21/16f2805de61662a4?w=412&h=482&f=gif&s=2234087)


### 2.Vue-Infinite-Scroll

如果您想在访问者到达底部时在网页上加载新元素，这会是一个很好且易于实现的指令。当滚动到页面底部时，将执行绑定到v-infinite-scroll的方法。

项目地址：`https://github.com/ElemeFE/vue-infinite-scroll`

安装方式：`npm install vue-infinite-scroll --save`
```
<template>
  /* your website code   */
  <div
    v-infinite-scroll="onLoadMore"
    infinite-scroll-disabled="busy"
    infinite-scroll-distance="10"
  ></div>
<template>

<script>
export default {
  data() {
    return {
      data [],
      busy: false,
      count: 0
    }
  },
  methods: {
    onLoadMore() {
      this.busy = true;
      setTimeout(() => {
        for (var i = 0, j = 10; i < j; i++) {
          this.data.push({ name: this.count++ });
        }
        this.busy = false;
      }, 1000);
    }
  }
}
</script>
```

### 3.Vue-Focus
有时在Web开发中，管理输入焦点可能很棘手。Vue-Focus可以帮助您直接从视图模型管理焦点。

项目地址：`https://github.com/simplesmiler/vue-focus`

安装方式：`npm install vue-focus --save`
```
<template>
  <button @click="focusedElement = true">Input gets Focus</button>
  <input type="text" v-focus="focusedElement">
</template>

<script>
export default {
  data: function() {
    return {
      focusedElement: false,
    };
  },
};
</script>
```
效果演示：


### 4.Vue-Blur
如果你的页面在访客尚未注册的时候，有些部分需要加上半透明遮罩，那么Vue-Blur是一个不错的选择。它还带有用于自定义参数的选项，例如不透明度，过滤器和状态之间的转换。

项目地址：`https://github.com/ndelvalle/v-blur`

安装方式：`npm install --save v-blur`
```
<template>
  /* Use this with a boolean value (true/false) */
  <div v-blur="isBlurred"></div>
  
  /* Use this with an object that uses values from the config */
  <div v-blur="blurConfig"></div>
</template>

<script>
  export default {
      data () {
        return {
          isBlurred: true, // activate and deactivate based on a boolean value
          blurConfig: {
            isBlurred: false, // activate and deactivate based on a boolean value and also provide a config
            opacity: 0.3,
            filter: 'blur(1.2px)',
            transition: 'all .3s linear'
          }
        }
      }
    }
  };
</script>
```

效果演示：

![](https://user-gold-cdn.xitu.io/2019/12/21/16f285da75153b0a?w=568&h=374&f=gif&s=134782)
### 5.V-Clipboard

这是一个小巧而实用的包，您可以使用它将值从元素复制到用户的剪贴板，而不必实现大量的逻辑。它可以复制静态或动态值，当元素被点击时，指令的值会被复制到剪贴板上。用户需要复制代码片段的时候，这个就排上用场。

项目地址：`https://github.com/euvl/v-clipboard`

安装方式：`npm install --save v-clipboard`
```
<template>
  <button v-clipboard='valueToCopy'>Copy to clipboard</button>
  // 当单击包含v-clipboard指令的元素时，valueToCopy的值将被复制到剪贴板中
</template>

<script>
export default {
  data() {
    return {
      valueToCopy: "Some Text"
    }
  }
};
</script>
```

### 6.Vue-ScrollTo

滚动元素从未如此简单！ 你可以监听元素上的点击事件，让浏览器滚动到给定的标签，这对于处理文章目录跳转和导航跳转非常有用！

项目地址：`https://github.com/rigor789/vue-scrollTo`

安装方式：`npm install --save vue-scrollto`
```
<template>
  <button v-scroll-to="'#element'">Scroll to #element as the target</button>
  <h1 id='element'>Hi. I'm element</h1>
</template>
```
效果演示：
![](https://user-gold-cdn.xitu.io/2019/12/21/16f28ba3b7e45fc3?w=467&h=132&f=gif&s=29774)

### 7.V-Hotkey
这个自定义指令可以使您可以轻松地将快捷键绑定到组件,而且可以给组件绑定一个或多个快捷键。比如你想要通过按下 `esc`键后隐藏某个组件，按住`ctrl+enter` 再显示它,下面代码便可轻松实现：

项目地址：`https://github.com/Dafrok/v-hotkey`

安装方式：`npm i --save v-hotkey`
```
<template>
  <div v-show="show"
       v-hotkey="{
      'esc': onClose,
      'ctrl+enter': onShow
    }">
    Hold `esc` to close me! Press `ctrl+enter` to open me!
  </div>
</template>

<script>
export default {
  data () {
    return {
      show: true
    }
  },
  methods: {
    onClose () {
      this.show = false
    },
    onShow () {
      this.show = true
    }
  }
}
</script>
```

### 8.V-Click-Outside

这是一个很棒的指令，可以在不停止事件传播的情况下对单击元素上的事件做出反应。这对于关闭对话框、菜单等非常有用。

项目地址：`https://github.com/ndelvalle/v-click-outside`

安装方式：`npm install --save v-click-outside`

```
<template>
  <div v-show="show" v-click-outside="onClickOutside">
    Hide the element if a click event outside is triggered
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: true
    };
  },
  methods: {
    onClickOutside() {
      console.log(11111);
      this.show = false;
    }
  }
};
</script>
```
效果演示：

![](https://user-gold-cdn.xitu.io/2019/12/21/16f28fbfa370c816?w=590&h=235&f=gif&s=33892)

### 9.V-Scroll-Lock

这个指令的作用是在打开模态浮层的时候防止下层的元素滚动。在处理多个设备时，这尤其有用！

项目地址：`https://github.com/phegman/v-scroll-lock`

安装方式：`npm install v-scroll-lock --save`

使用场景：`https://v-scroll-lock.peterhegman.com/`
```
<template>
  <div class="modal" v-if="open">
    <button @click="closeModal">X</button>
    <div class="modal-content" v-scroll-lock="open">
      <p>A bunch of scrollable modal content</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Modal',
  data () {
    return {
      open: false
    }
  },
  methods: {
    openModal () {
      this.open = true
    },
    closeModal () {
      this.open = false
    }
  }
}
</script>
```
### 10.V-Tooltip
Akryum的这个很酷的软件包为您提供了一个很棒的工具提示指令。只需将一些文本绑定到v-tooltip，就可以了。

项目地址：`https://github.com/Akryum/v-tooltip`

安装方式：`npm install --save v-tooltip`

```
<template>
  <div>
    <p>
      <input v-model="message" placeholder="Message">
    </p>
    <p>
      <span v-tooltip="message">{{ message }}</span>
    </p>
  </div>
</template>
```
### 总结
在本文中，我收集了10个很棒的自定义Vue指令，您可以在未来的项目中使用这些指令，而不必每次都自己重新造轮子。