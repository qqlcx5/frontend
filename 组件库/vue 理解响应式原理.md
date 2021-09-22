# Vue 理解响应式原理

## 前言
 JavaScript 中，数据分为两种类型：
 - 基本类型：`undefined`、`null`、`string`、`number`、`boolean`、`symbol`
引用类型：`Object`、`Array`、`RegExp`、`Date`、`Function`

基本类型的值是存放在**栈区**的，即内存中的栈内存

引用类型的值是同时保存在**栈内存和堆内存**的

`//...`

### 对于对象

```vue
<template>
  <div>
    <h5>名称：{{obj.name}}</h5>
    <button @click="handleA()">A更新</button>
    <button @click="handleB()">b更新</button>
    <button @click="handleForce()">强制更新</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      obj: {}
    }
  },
  methods: {
    handleA(){
      this.obj.name = 'A 星野'
      console.log(this.obj);
    },
    handleB(){
      this.$set(this.obj, 'name', 'B 星野')
      console.log(this.obj);
    },
    handleForce(){
      this.$forceUpdate()
    }
  }
}
</script>
```

思考一下，点击`handleA`、`handleB`、`handleForce`方法后，视图会有什么效果。

点击`handleA` 视图未变化，但打印数据输出`name` A星野 值。

点击`handleB`视图更新成`B 星野`，打印数据输出`name` B星野 值。

点击`handleA`后，在点击`handleForce` 后视图更新，但在修改name值并不会更新。

其实新增属性`name`值属于非响应式值，所以当你点击`handleA`虽然属性值改了，但是并未通知视图更新。

`this.$set(object, propertyName, value)` API 向响应式对象中添加一个 属性，并确保这个新增的属性同样是响应式的，且触发视图更新。

`this.$forceUpdate()`迫使Vue实例重新渲染，但并为将`name`添加成响应式属性，所以在修改值视图并不会更新。

假设我需要赋值多个新属性情况下，按照上面的写法是不是太冗余了，其实我们可以通过`Object.assign()` 或 `_.extend()`
来更新数据。

```js
this.obj = Object.assign(this.obj, { a: 1, b: 2 })
// 代替上面这种方法。
this.obj = Object.assign({}, this.obj, { a: 1, b: 2 })
```

### 对于数组

```vue
<template>
  <div>
    <h5>名称：{{arr}}</h5>
    <button @click="handleA()">A更新</button>
    <button @click="handleB()">b更新</button>
    <button @click="handleSplice()">splice更新</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      arr: ['a', 'b', 'c']
    }
  },
  methods: {
    handleA(){
      this.arr[0] = 'A 星野'
      console.log(this.arr);
    },
    handleB(){
      this.$set(this.arr, 0, 'B 星野')
      console.log(this.arr);
    },
    handleSplice(){
      this.arr.splice(0, 1, 'splice 星野')
      console.log(this.arr);
    },
  }
}
</script>
```

思考一下，点击`handleA`、`handleB`、`handleSplice`方法后，视图会有什么效果。

点击`handleA` 视图没有更新。

点击`handleB`视图更新成`B 星野`

点击`handleSplice`后，视图更新成`splice 星野`

其实Vue不能检测以下数组的变动：

- 当你利用索引直接设置一个数组项时，例如：`this.arr[index] = newValue`
- 当你修改数组的长度时，例如：`this.arr.length = newLength`

处理上面情况一的问题。通常可以通过`Vue.set`和`splice`方法就能够解决。

```
// Vue.set 
this.$set(this.arr, index, newValue)

// Array.prototype.splice
this.splice(index, 1, newValue)
```

情况二的话，也可以通过`splice`解决。

```
this.splice(newLength)
```

显然大家会有很多疑惑，为什么Vue中Data为对象新增属性值需要通过`$set`API来更新，或者用`$forceUpdate()`更新（官网不推荐使用），为什么数组通过下标修改数组某个值，视图不更新，也需要`$set`和`splice`触发更新。带着疑问往下看

### 响应式原理

我们都知道 `Vue.js` 是一款 MVVM 框架，由三个重要部分构成

* 数据层（Model）：数据及业务逻辑
* 视图层（View）：展示数据渲染后的界面
* 业务逻辑层（ViewModel）：负责将数据与视图关联起来

**理解ViewModel**

它的主要职责就是：

* 数据变化后更新视图
* 视图变化后更新数据

当然，它还有两个主要部分组成

监听器（Observer）：对所有数据的属性进行监听
解析器（Compiler）：对每个元素节点的指令进行扫描跟解析,根据指令模板替换数据,以及绑定相应的更新函数

**双向绑定原理**

1. 首先组件创建时会实例化`new Vue()`，然后对`data`进行`Object.defineProperty()`，这个过程发生Observe中
2. 同时对模板执行编译，收集绑定的`data`数据，这个过程发生在Compile中
3. 然后定义⼀个更新函数和Watcher，将对应数据变化时Watcher会调用更新函数
4. 如果data中数据⼀旦发生变化，会首先找到对应的Dep，通知所有Watcher执行更新函数

![](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210718-16265364361009.jpg)

```js
function render() {
  console.log('模拟视图渲染')
}
// data数据 
let obj = { name: '星野', location: { x: 100, y: 10 }

// 递归对象，因为对象里面可能包含对象
function observer(obj) {
  if (typeof obj == 'object') {
    for (let key in obj) {
      defineReactive(obj, key, obj[key])
    }
  }
}
// 第二步 给对象 添加 Object.defineProperty，当数据更新触发更新函数更新视图
function defineReactive(obj, key, value) {
  observer(value)
  Object.defineProperty(obj, key, {
    // 得到value值
    get() { 
      return value
    },
    // 数据更新会触发set函数
    set(newValue) {
      // newValue 新的值有可能是个对象，对象有继续触发defineReactive 函数
      observer(newValue) 
      if (value !== newValue) {
        render() // 通知视图更新
        value = newValue
      }
    },
  })
}

function $set(obj, key, value) {
  defineReactive(obj, key, value)
}
// 第一步，初始化data数据
observer(obj)

// $set(obj, 'a', 5);
// obj.a = 100;
// console.log(obj.a);

// obj.location= { x: 1000, y: 1000 }
// console.log(obj.location);
```

上面的代码简单的实现了如何监听数据的的事件，但在`set`定义的自定义的函数并不会主动触发执行，只有对模板执行编译后对绑定的data数据进行依赖收集，数据更新的时候触发自定义函数更新。

```
<div>
    {{obj.name}}
</div>
```
当对模版进行编译时，会对`obj.name`进行依赖收集。

**Object.defineProperty** 的缺陷

当我们给对象新增属性并不会触发组件的重新渲染，因为 `Object.defineProperty` 不能拦截到这些操作。也就为什么我们要通过`$set`API来对数据响应式处理，其实本质就是通过`defineReactive`函数对数据`Object.defineProperty`，这样数据通知视图更新了。

```
function $set(obj, key, value) {
  defineReactive(obj, key, value)
}
```

### 对于数组

对于对象我们可以通过`Object.defineProperty`，但是数组并不支持这个方法，所以我们就要重写数组，在保留数组原有功能的同时又能对数组中数据更新触发视图更新。

```js
function render() {
  console.log('模拟视图渲染')
}

let arr = [1, 2, 4]

let methods = ['pop', 'push', 'shift', 'unshift', 'sort', 'reverve', 'splice']
let arrayProto = Array.prototype
let proto = Object.create(arrayProto)
methods.forEach(method => {
  proto[method] = function() {
    render()
    arrayProto[method].call(this, ...arguments)
  }
})

function observer(obj) {
  if (Array.isArray(obj)) {
    obj.__proto__ = proto
    return
  }
}

function defineReactive(obj, key, value) {
  observer(value)
  Object.defineProperty(obj, key, {
    get() {
      return value
    },
    set(newValue) {
      observer(newValue)
      if (value !== newValue) {
        render()
        value = newValue
      }
    },
  })
}
observer(obj)
function $set(obj, key, value) {
  defineReactive(obj, key, value)
}

arr.push(123)
```

我们需要理解一下面向切面编程概念（AOP），就能更好的理解上面的代码。

> 面向切面编程（AOP是Aspect Oriented Program的首字母缩写） ，我们知道，面向对象的特点是继承、多态和封装。而封装就要求将功能分散到不同的对象中去，这在软件设计中往往称为职责分配。实际上也就是说，让不同的类设计不同的方法。这样代码就分散到一个个的类中去了。这样做的好处是降低了代码的复杂程度，使类可重用。     
> 但是人们也发现，在分散代码的同时，也增加了代码的重复性。什么意思呢？比如说，我们在两个类中，可能都需要在每个方法中做日志。按面向对象的设计方法，我们就必须在两个类的方法中都加入日志的内容。也许他们是完全相同的，但就是因为面向对象的设计让类与类之间无法联系，而不能将这些重复的代码统一起来。    
> 也许有人会说，那好办啊，我们可以将这段代码写在一个独立的类独立的方法里，然后再在这两个类中调用。但是，这样一来，这两个类跟我们上面提到的独立的类就有耦合了，它的改变会影响这两个类。那么，有没有什么办法，能让我们在需要的时候，随意地加入代码呢？这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。

```js
let methods = ['pop', 'push', 'shift', 'unshift', 'sort', 'reverve', 'splice']
let arrayProto = Array.prototype
let proto = Object.create(arrayProto)
methods.forEach(method => {
  proto[method] = function() {
    render()
    arrayProto[method].call(this, ...arguments)
  }
})
```

`arrayProto` 继承原有数组的原型，然后对数组原型中`'pop', 'push', 'shift', 'unshift', 'sort', 'reverve', 'splice'`添加自定义函数，当通过上面的方法更新数组时，会进行依赖收集，当必要的时候通知视图更新。

虽然这样实现响应式更新，但也存在缺陷。

- 当你利用索引直接设置一个数组项时，例如：`this.arr[index] = newValue`
- 当你修改数组的长度时，例如：`this.arr.length = newLength`

参考

- [Vue数据双向绑定](https://juejin.cn/post/6844903942254510087#heading-9)
- [什么是面向切面编程AOP](https://www.zhihu.com/question/24863332/answer/48376158)
- [廖雪峰博客](https://www.liaoxuefeng.com/wiki/1022910821149312/1109527162256416)
- [珠峰](http://www.zhufengpeixun.com/grow/)