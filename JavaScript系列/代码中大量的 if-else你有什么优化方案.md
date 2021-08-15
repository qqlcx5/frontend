# 代码中大量的 if-else，你有什么优化方案?

## 前言

在产品快速迭代的中，由于追求开发速度，我们往往忽略代码的可读性与扩展性，不合理的使用`if-else`条件判断会使我们的程序复杂度大大提升，同时也会使代码的可读性急速下降，后期维护难度也大大提高，真的让人脑壳疼。比如下方示例：

```js
// 贷款申请操作的处理
function check() {
  // 是否输入正确用户名
  if (this.checkUsername(this.username)) {
    // 是否输入正确身份证号
    if (this.checkIdCard(this.idCard)) {
      // 请输入正确的电话号码
      if (this.checkTel(this.tel)) {
        // 担保人是本人
        if (this.dbr === '担保人是本人') {
          // 是否存在身份证正面
          if (document.querySelector('.sfzzm img')) {
            console.log('存在身份证正面')
            // 是否存在身份证反面
            if (document.querySelector('.sfzfm img')) {
              console.log('存在身份证反面')
              // 是否存在学历证书
              if (document.querySelector('.xlzs img')) {
                console.log('存在学历证书')
                if (this.ydxy) {
                  this.tijiaoIsShow = false
                }
              } else {
                Toast('请上传学历证书')
                this.tijiaoIsShow = true
              }
            } else {
              Toast('请上传身份证反面')
            }
          } else {
            Toast('请上传身份证正面')
          }
        } else if (this.dbr == '担保人不是本人') {
          if (this.checkUsername(this.dbrname)) {
            if (this.checkIdCard(this.dbridCard)) {
              if (this.checkTel(this.dbrzyzh)) {
                if (document.querySelector('.sfzzm img')) {
                  console.log('存在身份证正面')
                  if (document.querySelector('.sfzfm img')) {
                    console.log('存在身份证反面')
                    if (document.querySelector('.xlzs img')) {
                      console.log('存在学历证书')
                      this.tijiaoIsShow = false
                    } else {
                      Toast('请上传学历证书')
                    }
                  } else {
                    Toast('请上传身份证反面')
                  }
                } else {
                  Toast('请上传身份证正面')
                }
              } else {
                Toast('请输入担保人展业证号')
              }
            } else {
              Toast('请输入担保人身份证号')
            }
          } else {
            Toast('请输入担保人姓名')
          }
        } else {
          Toast('请选择担保人是否为本人')
        }
      } else {
        Toast('请输入正确的电话号码')
      }
    } else {
      Toast('请输入正确的身份证号')
    }
  } else {
    Toast('请输入正确的姓名')
  }
}
```

如果你接手这样一段代码，相信大家和我的心情是一样的：

![AA61E5F958A98A63ACEF3EAAD9E2E87D](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9be4a6fefb74653af37c2cf61101922~tplv-k3u1fbpfcp-zoom-1.image)

本文归纳以下几种优化`if-else`场景，希望对大家有所帮助。

- 单个 if 语句优化
- if/else 语句优化
- 单个 if 多条件优化
- 多个 else if 分支优化

## 单个 if 语句优化

**优化前**

```js
if (flag) {
  this.handleFn()
}
```

**优化后**

```js
flag && this.handleFn()
// handleFn是普通函数
```

这种写法就比较清晰，简洁，好读！

另外如果遇到有很多的`if`语句，但是执行的功能函数却是一致的情况，我们可以用”逻辑与“或者”逻辑或“来把他们合并成一个表达式。如果我们这些彼此独立的条件判断可以被视为同一次检查的场景时，一次检查的意图明显在可读性上优于多次的条件检查。我们来看一段代码片段:

```js
if (!(staffInfo.patientName && staffInfo.phone)) {
  // doSomething
}
...
if (!(staffInfo.phone && staffInfo.idCardNum)) {
  // doSomething
}
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bfbf4bd6fcdf4a76af0dbec4619c3537~tplv-k3u1fbpfcp-watermark.image)

我们可以通过**合并逻辑**，将上面代码修改为如下：

```js
if (!(staffInfo.patientName && staffInfo.phone) || !(staffInfo.phone && staffInfo.idCardNum)) {
  // doSomething
}
```

## if/else 语句优化

`if/else`可以说在项目中遇到频率是最高，通常可以这两种策略优化

- 排非策略
- 三元运算符

### 排非策略

比如用户登录场景，如果用户名和密码输入框为空，那么我们就提示用户”用户名和密码不能为空”；如果有值，就执行登录的操作。

**优化前**

```js
if (user && password) {
  // 逻辑处理
} else {
  throw '用户名和密码不能为空!'
}
```

**优化后**

```js
if (!user || !password) return throw '用户名和密码不能为空!'
// 逻辑处理
```

表单提交时，需要提前排除那些提交不规范的内容，通常情况下，表单提交遇到不符合我们要求大于我们提交成功的情形，排非策略是个很不错的选择。

### 三元运算符

**示例一**

```
let allow = null
if（age >= 18）{
   allow = '通过';
} else {
   allow = '拒绝';
}

// 优化后
let allow = age >= 18 ? '通过' : '拒绝'
```

**示例二**

```js
if (flag) {
  success()
} else {
  fail()
}

//优化后
flag ? success() : fail()
```

三元运算符相比`if/else`来说，只需一行语句，代码简练精炼。

## 单个 if 多条件优化

**优化前**

```js
function test(type) {
  if (type === 'jpg' || type === 'png' || type === 'gif' || type === 'svg') {
    console.log('该文件为图片')
  }
}
```

**优化后**

```js
function test(type) {
  const imgArr = ['jpg', 'png', 'gif', 'svg']
  if (imgArr.includes(type)) {
    console.log('该文件为图片')
  }
}
```

## 多个 else if 分支优化

多个`else if`通常是一个糟糕的选择，它导致设计复杂，代码可读性差，并且可能导致重构困难。

```js
if (this.type === 'A') {
  this.handleA()
} else if (this.type === 'B') {
  this.handleB()
} else if (this.type === 'C') {
  this.handleC()
} else if (this.type === 'D') {
  this.handleD()
} else {
  this.handleE()
}
```

我们经常遇到多个`else if`条件判断代码，随着逻辑复杂性的增加，`else if`的代码将变得越来越肿。

看一下逻辑绘制为流程图

![if](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b387916435e45019915dfb6d8efa28a~tplv-k3u1fbpfcp-zoom-1.image)

从上面的流程图可以看出，不同条件分支的代码具有很高的耦合度。先前的条件判断将影响后续的代码流，并且此类代码在后续开发中难以维护。我们可以通过`switch`、`key-value`和`Map`来优化代码。

### switch

```js
switch (val) {
  case 'A':
    handleA()
    break
  case 'B':
    handleB()
    break
  case 'C':
    handleC()
    break
  case 'D':
    handleD()
    break
}
```

看一下逻辑绘制为流程图

![switch](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32f52859224a46fc96fbcf7f83bbd023~tplv-k3u1fbpfcp-zoom-1.image)

流程图显然更简单。而且，不同的条件分支之间没有嵌套，并且它们彼此独立。逻辑很清楚。

### key-value

虽然`switch`语句在逻辑上确实比`else if`语句简单，但是代码本身也有点多。

其实我们对象枚举，将条件与特定操作相关联的键值。

```js
let enums = {
  A: handleA,
  B: handleB,
  C: handleC,
  D: handleD,
  E: handleE,
}
function action(val) {
  let handleType = enums[val]
  handleType()
}
```

流程图：

![enums](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7cefa9fdf2c4fc9860c579697dd830d~tplv-k3u1fbpfcp-zoom-1.image)

这种方式**消除了所有条件语句，并使用键值对象存储条件和操作之间的关系**。当我们需要根据条件执行代码时，我们不再需要使用`else if`或`switch`语句来处理相应的动作，我们只需从中提取相应的函数 handleType 并执行它即可。

### Map

实际上我们还可以通过`Map`来进一步的优化我们的代码。

对比`Object`的话，`Map`具有许多优点

- 对象的键只能是字符串或符号，而`Map`的键可以是任何类型的值。
- 我们可以使用`Map size`属性轻松获取`Map`的键/值对的数量，而对象的键/值对的数量只能手动确定。
- 具有极快的查找速度。

上面的例子可以优化如下：

```js
let enums = new Map([
  ['A', handleA],
  ['B', handleB],
  ['C', handleC],
  ['D', handleD],
  ['E', handleE],
])

function action(val) {
  let handleType = enums(val)
  handleType()
}
```

如果我们遇到**多层复杂条件**，`Map`语句优势就更明显了！

```js
if (mode == 'kline') {
  if (this.type === 'A') {
    this.handleA()
  } else if (this.type === 'B') {
    this.handleB()
  } else if (this.type === 'C') {
    this.handleC()
  } else if (this.type === 'D') {
    this.handleD()
  }
} else if ((mode = 'depth')) {
  if (this.type === 'A') {
    this.handleA()
  } else if (this.type === 'B') {
    this.handleB()
  } else if (this.type === 'C') {
    this.handleC()
  } else if (this.type === 'D') {
    this.handleD()
  }
}
```

对于上述如此复杂的场景，是否可以通过`Map`来进行优化？

其实我们只需要将不同的判断语句连接成一个字符串，以便我们可以将条件和操作以键值格式关联在一起。

```js
let enums = new Map([
  ['kline_A', handleKlineA],
  ['kline_B', handleKlineB],
  ['kline_C', handleKlineC],
  ['kline_D', handleKlineD],
  ['kline_E', handleKlineE],
  ['depth_A', handleDepthA],
  ['depth_B', handleDepthB],
  ['depth_C', handleDepthC],
])

function action(mode, type) {
  let key = `${mode}_${type}`
  let handleType = enums(key)
  handleType()
}
```

瞬间简单明了很多，有木有～～～

## 参考文章

- [if-else 重构优化](https://jelly.jd.com/article/5fe993226c8ad2014c3f0ecc)
- [if-else 逻辑判断优化](https://ths.js.org/2020/09/30/if-else%E9%80%BB%E8%BE%91%E5%88%A4%E6%96%AD%E4%BC%98%E5%8C%96/)
- [How to Use if-else](https://betterprogramming.pub/sorry-if-else-statements-arent-bad-at-all-472c9841eea)
- [Javascript 条件语句优化策略](https://www.jianshu.com/p/6ec90a969878)
- [深入浅出代码优化﹣if/else](https://juejin.cn/post/6844903696443113486)
