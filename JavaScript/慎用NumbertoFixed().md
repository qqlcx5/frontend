---
title: 慎用Number.toFixed()
date: 2021-7-28
tags:
- JavaScript
---
## 引言

最近在公司项目中碰到一个隐藏的 bug，调试许久才发现竟然是 toFixed 函数精度问题引起的，从而引发了我一系列的思考。

我们都知道，计算机在二进制环境下，浮点数的计算精度会存在缺失问题，最经典的例子就是为什么 0.1+0.2 不等于 0.3？

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be8ec0c6599d4bb8b950568b0613f5be~tplv-k3u1fbpfcp-watermark.image)

为了解决上述问题，我们自然而然会想到 toFixed 方法来四舍五入，可结果却不如人意！

## toFixed()的精度问题

我们来看一下 toFixed()在 chrome、火狐、IE 浏览器下的不同表现：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc2b27176ba147aab00d333b854b5ed3~tplv-k3u1fbpfcp-watermark.image)

可以看到 toFixed()的四舍五入在 chrome、火狐上并不准确。
而 toFixed()在 chrome、火狐上也并不是网上流传甚广的用银行家舍入法来进行四舍五入的。

> 银行家舍入法的规则是“四舍六入五考虑，五后非零就进一，五后为零看奇偶，五前为偶应舍去，五前为奇要进一”。

例如银行家舍入法在 (2.55).toFixed(1) = 2.5、(3.55).toFixed(1) = 3.5 上就不符合了。

翻阅[ecmascript 规范](https://262.ecma-international.org/6.0/#sec-number.prototype.tofixed)对 toFixed 的表述如下：

![Number.prototype.toFixed](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/56fb7cd1c40b4d4b9c08ef3f463d4639~tplv-k3u1fbpfcp-watermark.image)

上面规范这段大概意思就是如果 toFixed 的入参小于 10 的 21 次方，那么就取一个整数 n，让 n\*10^f - x 的精确值尽可能的趋近于 0，如果存在两个这样的 n,取较大的 n。这段话可能有点晦涩难懂，我们举个例子比如 1.335.toFixed(2)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c8e3040ae7644faa5ec7b4ef82377d2~tplv-k3u1fbpfcp-watermark.image)

上图例子中 1.335.toFixed(2)按照四舍六入五成双应该是 1.34，但是实际情况确实 1.33。这是因为 n=133 的时候让 n\*10^f - x 更趋近于 0，所以最后得到的结果是 1.33。

## 解决方法

## 参考文章

- [toFixed 四舍五入的不准确性](https://juejin.cn/post/6927215610552123406)
- [JS toFixed 及其缺陷和解决方法](https://www.daimajiaoliu.com/daima/479775a03100402)
- [toFixed 计算错误解决方法](http://www.chengfeilong.com/toFixed)
- [那些年在 JS 中踩过 Number.toFixed(n)的坑](https://www.iwangzhu.top/article/14)
- [JS 四则运算与四舍五入精度问题及解决方案](https://juejin.cn/post/6844903703565041678)
