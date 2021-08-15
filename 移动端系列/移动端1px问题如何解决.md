# 移动端1px问题如何解决？

1px 问题在实际面试中，尤其是大厂面试中出现的频率是比较高的。很多同学简历上虽然写了移动端的开发经验，但实际面试时被问到“1px 问题怎么解决”时却压根不知道面试官在说啥，这样难免会让面试官质疑你对移动端开发这个命题的深入程度。

## 1px 问题的起因

在`Retina`高清屏幕，那么`dpr`的值可能为2或者3，那么当你在pc端上看到的1px时，在移动端上看到的就会是2px或者3px。


**物理像素(physical pixel)**

![physical](https://i.loli.net/2021/04/11/WF6cnBsYNGb98CQ.jpg)

物理像素又被称为设备像素，他是显示设备中一个最微小的物理部件。每个像素可以根据操作系统设置自己的颜色和亮度。可以通过API`screen.width/height`读取

**设备独立像素(density-independent pixel)**

设备独立像素也称为密度无关像素，可以认为是计算机坐标系统中的一个点，这个点代表一个可以由程序使用的虚拟像素(比如说CSS像素)，然后由相关系统转换为物理像素。

**设备像素比(device pixel ratio)**

设备像素比简称为`dpr`，其定义了物理像素和设备独立像素的对应关系。它的值可以按下面的公式计算得到：

```
设备像素比 ＝ 物理像素 / 设备独立像素
```

在PC端上，像素可以称为CSS像素，PC端上dpr为1。也就说你书写css样式是是多少在pc上就显示多少。而在移动端上，像素通常使用设备像素。

一个物理像素等于多少个设备像素取决于移动设备的屏幕特性(是否是**Retina**)和**用户缩放比例**。

大家可以尝试打开自己的 Chrome 浏览器，启动移动端调试模式，然后尝试在控制台去输出这个 `devicePixelRatio` 的值。这里我选中了 `iPhone6/7/8` 这系列的机型，输出的结果就是2：

![-w967](https://i.loli.net/2021/05/05/vOZmzkFVXJ1jMcn.jpg)

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
| 设置viewport解决问题 | 一套代码，所有页面 | 缺点不明显|



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

这个方法的可行性会更高，兼容性也更好。唯一的缺点是代码会变多。

思路是先放大、后缩小：在目标元素的后面追加一个 ::after 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。

```
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

设计稿：750的情况下，`100px`写成`1rem`，1px还是写成1px。

总结，新项目最好使用的是`设置viewport解决问题`，这个方法兼容性好，后期写起来方便。，其次用的比较多的方法就是`伪元素`的方法。 其他的背景图片，阴影的方法毕竟还是不太灵活，而且兼容性不好。



### 参考文章

- [A tale of two viewports](https://quirksmode.org/mobile/viewports.html)
- [手淘H5页面的终端适配](https://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html)
- [1px](https://github.com/freedomcly/1px)