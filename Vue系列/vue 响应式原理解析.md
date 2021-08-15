# Vue 响应式原理解析


```js
function render() {
  console.log('模拟视图渲染')
}
// let obj = { name: 'jss', location: { x: 100, y: 10 }, arr: [1,2,3] };
let obj = [1, 2, 4]

/**
 * Array
 */

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
  if (typeof obj == 'object') {
    for (let key in obj) {
      defineReactive(obj, key, obj[key])
    }
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
// $set(obj, 'a', 5);
// obj.a = 100;
// console.log(obj.a);

// obj.location= { x: 1000, y: 1000 }
// console.log(obj.location);

obj.push(1123)
```
