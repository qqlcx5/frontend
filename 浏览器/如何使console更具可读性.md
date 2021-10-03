---
title: 如何使console更具可读性
date: 2020-05-28 00:00:00
tags:
- 浏览器
---

作为Web开发人员，用`console.log`调试代码应该已经是习以为常的事。但是控制台提供了许多其他方法，可以帮助您更好地进行调试。

如图所示，`console`还有以下这些方法。

16217771251356/16217771368828.jpg)

以上这些方法，可以分为两类：

* 标准的特性
* 非标准的特性


## 标准的特性

以下七种方法，可用生产环境。

* 显示信息
* 占位符
* 分组
* 表格
* 计时功能
* 断言

### 一、显示信息

1. `console.log("这是log")` 输出普通信息
2. `console.info("这是info")` 输出提示性信息
3. `console.warn("这是warn")` 输出警示信息
4. `console.error("这是error")` 输出错误信息

控制台显示如下

![-w564](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16217811906246.jpg)

### 二、占位符

上面4种显示信息方法都可以使用`printf`风格的占位符，占位符的种类比较少，只支以下这四种。

* `%s` 字符串
* `%d` `%i` 整数
* `%f` 浮点数
* `%o` 对象

**示例**
```
console.log('this color is %s', "red");
console.log("%d年%d月%i日",2021,5,25)
console.log("圆周率是%f",3.1415926)
let dog = { name: "小白", color: "white" }
console.log("%o",dog);
```

控制台显示如下

![-w566](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16217870687221.jpg)

### 三、分组

如果信息太多，可以将其分组显示。

```
console.group('用户信息');
console.log('名字: 李白');
console.log('工作: Acmen');
// 嵌套
console.group('地址');
console.log('福建省');
console.log('厦门市');
console.log('思明区');
console.groupEnd();
console.groupEnd();
```

![-w573](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16217877548147.jpg)

如果要默认折叠组，也可以使用`console.groupCollapsed()`代替`console.group()`

### 四、表格

将数据以表格的形式显示。

```
console.table([
    { id: 12987122, name: '蛋糕', time: '2016-05-02' },
    { id: 12987125, name: '冰淇淋', time: '2016-05-09' }
])
```

![-w809](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16219589145514.jpg)

### 五、计时功能

`console.time()`和`console.timeEnd()`，用来运行某串代码的需要花费的时间。

![-w500](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16218698820290.jpg)

### 六、断言

`console.assert()`用来判断一个表达式或变量是否为真。如果结果为否，则在控制台输出一条相应信息，并且抛出一个异常。

![-w499](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16218694254402.jpg)

## 非标准的特性

该特性是非标准的，请尽量不要在生产环境中使用它！
### 一、性能分析

分析程序各个部分的运行时间，找出问题所在。

假设有一个函数`Foo()`，里面调用了另外两个函数`funcA()`和`funcB()`，其中`funcA()`调用10次，`funcB()`调用1次。

```js
function Foo() {
    for (let i = 0; i < 10; i++) { funcA(1000); }
    funcB(10000);
}

function funcA(count) {
    for (let i = 0; i < count; i++) { }
}

function funcB(count) {
    for (let i = 0; i < count; i++) { }
}
```

![-w579](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16218711710151.jpg)

新的浏览器这个方法已无效。

![-w578](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16219593214525.jpg)

### 二、 console.dir()

`console.dir()`可以显示一个对象所有的属性和方法。

![-w427](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16218720787133.jpg)

### 三、计数器

`console.count()`用于计数，输出它被调用了多少次。

![-w725](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-16218716887783.jpg)

### 参考文献

- [MDN console](https://developer.mozilla.org/zh-CN/docs/Web/API/Console)
- [console的正确打开方式](https://jelly.jd.com/article/6063e6cdd96486017129cb36)
- [Chrome DevTools之console篇和命令菜单篇](https://jelly.jd.com/article/5f3cdb5a0304350151885901)
- [How to use the JavaScript console: going beyond console.log()](https://medium.com/free-code-camp/how-to-use-the-javascript-console-going-beyond-console-log-5128af9d573b)
