#  移动端1px问题

在移动端web开发中，UI设计稿中设置边框为1像素，前端在开发过程中如果出现border:1px，测试会发现在retina屏机型中，1px会比较粗，即经典的移动端1px像素问题。

1px 问题在实际面试中，尤其是大厂面试中出现的频率是比较高的。本文就探讨几种1px问题解决方案。

## 1px 问题的起因


1px 问题指的是在一些 Retina屏幕 的机型上，移动端页面的 1px 会变得很粗，呈现出不止 1px 的效果。

原因很简单——CSS 中的 1px 并不能和移动设备上的 1px 划等号。它们之间的比例关系有一个专门的属性来描述：
```javascript
// 设备像素比
window.devicePixelRatio = 设备的物理像素 / CSS像素
```

一个物理像素等于多少个设备像素取决于移动设备的屏幕特性(是否是**Retina**)和**用户缩放比例**。

大家可以尝试打开自己的 Chrome 浏览器，启动移动端调试模式，然后尝试在控制台去输出这个 `devicePixelRatio` 的值。这里我选中了 `iPhone6/7/8` 这系列的机型，输出的结果就是2：

![-w967](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/36de6501cd5041bf85cf764f0619e5c1~tplv-k3u1fbpfcp-zoom-1.image)

这就意味着我设置的 1px CSS 像素，在移动端上会用 2 个物理像素来进行渲染，所以实际看到的一定会比 1px 粗一些。


## 解决方案

1px 问题的解决方案是其实非常多的。不过从实用的角度出发，建议大家掌握3～4种就可以了，其他方法了解一下就行。

| 方案  | 优点 | 缺点  |
| --- | --- | --- |
| 直接写 0.5px | 代码简单 |  IOS及Android老设备不支持 |
| 用图片代替边框 | 全机型兼容  | 修改颜色及不支持圆角  |
| background渐变 | 全机型兼容  | 代码多及不支持圆角 |
| box-shadow模拟边框实现 | 全机型兼容 | 有边框和虚影无法实现  |
| 伪元素先放大后缩小 | 简单实用  | 缺点不明显  |
| 设置viewport解决问题 | 一套代码适用所有页面 | 缺点不明显|



### 1.直接写 0.5px

在 `WWDC`大会上，对`ios8+`的并且是`DPR=2`的设备来说，给出来了`1px`方案，当写 `0.5px`的时候，就会显示一个物理像素宽度的 `border`。 所以在iOS下，你可以这样写

```
border:0.5px solid #E5E5E5
```

虽然解决问题了，但是实用性不高，首先，得考虑IOS 系统需要8及以上的版本，安卓系统则有不兼容问题。

### 2.用图片代替边框

```
border: 1px solid transparent;
border-image: url('xxx.jpg') 2 repeat;
```

虽然解决问题了，但是后期样式调整会让人奔溃，比如颜色调整得UI小伙伴重新上传图片，然后又要修改代码，或者直接文件替换有涉及到图片缓存问题，如果后期来了一个要有边框圆角需求完全没法搞。


### 3.background渐变

```
background-position: left top;
background-image: -webkit-gradient(linear,left bottom,left top,color-stop(0.5,transparent),color-stop(0.5,#e0e0e0),to(#e0e0e0));
```

代码多，展示的边框实际是在原本的border空间内部的，如果元素背景色有变化的样式, 边框线也会消失；最后也不能适应圆角样式。

### 4.box-shadow模拟边框实现	

```
box-shadow: 0  -1px 1px -1px #e5e5e5,   //上边线
            1px  0  1px -1px #e5e5e5,   //右边线
            0  1px  1px -1px #e5e5e5,   //下边线
            -1px 0  1px -1px #e5e5e5;   //左边线
```

毕竟展示的阴影和边框一个样，但如果有边框还要有虚影样式就没法搞，鱼和熊掌不可兼得。


### 5.伪元素先放大后缩小

这个方法的可行性会更高，兼容性也更好。

实现方式：在目标元素的后面追加一个 ::after 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。

```JavaScript
.hairline{
  position: relative;
  &::after{
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    height: 1px;
    width: 100%;
    transform: scaleY(0.5);
    transform-origin: 0 0;
    background-color: #EDEDED;
  }
}
```
目前大部分移动端UI采用该方案，全机型兼容。

### 6.设置viewport解决问题

利用viewport+rem+js 实现的，边框`1px`直接写上自动转换。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" id="WebViewport" content="initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no" />
        <title>Document</title>
        <style type="text/css"></style>
    </head>
    <body>
        <script type="text/javascript">
            let viewport = document.querySelector('meta[name=viewport]')
            //下面是根据设备像素设置viewport
            if (window.devicePixelRatio == 1) {
                viewport.setAttribute('content', 'width=device-width,initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no')
            }
            if (window.devicePixelRatio == 2) {
                viewport.setAttribute('content', 'width=device-width,initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no')
            }
            if (window.devicePixelRatio == 3) {
                viewport.setAttribute('content', 'width=device-width,initial-scale=0.3333333333333333, maximum-scale=0.3333333333333333, minimum-scale=0.3333333333333333, user-scalable=no')
            }
            function resize() {
                let width = screen.width > 750 ? '75px' : screen.width / 10 + 'px'
                document.getElementsByTagName('html')[0].style.fontSize = width
            }
            window.onresize = resize
        </script>
    </body>
</html>
```

这种方式，优点很明显，全机型兼容，直接写1px简单方便！

## 总结
新项目最好使用的是`设置viewport解决问题`，这个方法兼容性好，后期写起来方便，其次用的比较多的方法就是`伪元素`的方法。 其他的背景图片，阴影的方法毕竟还是不太灵活，而且兼容性不好。




## 参考文章

- [A tale of two viewports](https://quirksmode.org/mobile/viewports.html)
- [解锁前端面试体系核心攻略](https://www.imooc.com/read/70)
- [手淘H5页面的终端适配](https://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html)
- [1px方案](https://github.com/freedomcly/1px)